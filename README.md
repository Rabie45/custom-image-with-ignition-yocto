# custom-image-with-yocto-ignition
Creating a custom Yocto image that includes Ignition requires several steps. You'll need to configure Yocto to build a custom image with all the necessary components, including Ignition. 

## What is ignition?
- Ignition is a comprehensive industrial automation software platform developed by Inductive Automation. It is widely used in manufacturing and industrial settings for Supervisory Control and Data Acquisition (SCADA), Human-Machine Interface (HMI), and Industrial Internet of Things (IIoT) applications.
### Key Features of Ignition:
- SCADA and HMI: Ignition is primarily used for creating SCADA systems that allow operators to monitor and control industrial processes. It also provides tools to create HMIs that enable interaction with machinery and equipment.
- Unlimited Licensing Model: One of Ignition's standout features is its licensing model, which allows unlimited clients, tags, and connections for a flat fee, making it cost-effective for large-scale deployments.
- Web-Based Interface: Ignition's interface is web-based, meaning it can be accessed from any device with a web browser, making it highly versatile and accessible.
-  IIoT Capabilities: Ignition supports IIoT by integrating with various industrial protocols, databases, and cloud services, allowing seamless data collection, processing, and analysis.
- Modularity: Ignition is highly modular, with various add-ons and modules that can be used to extend its functionality, such as reporting, alarm notifications, and enterprise administration.
- Cross-Platform: Ignition runs on Windows, Linux, and macOS, providing flexibility in deployment.
- Database Integration: It integrates with SQL databases, enabling powerful data logging, retrieval, and analysis capabilities.
- Scripting and Automation: Ignition supports Python scripting, allowing users to automate tasks, customize functionalities, and create complex logic within the platform.
### Use Cases:
-  Manufacturing: Monitoring and controlling production lines, tracking performance metrics, and managing alarms.
    Energy Management: Managing energy usage, monitoring critical systems, and ensuring compliance with energy standards.
    Water Treatment: Monitoring water quality, controlling treatment processes, and ensuring regulatory compliance.
    Building Automation: Managing HVAC systems, lighting, and security systems within buildings.

For more information https://inductiveautomation.com/ignition/

## Add ignition to the customized image
- You have two ways:
- The hard way to install the requirements to the ```local.conf``` file and to create the recipe to install ```ignition``` locally
- the easy way to add docker to the custom image and pull the docker image from ```docker hub```

### The easy way 
#### First let's add docker
- Let's add the meta-virtualization layer first go to poky ```git clone https://git.yoctoproject.org/meta-virtualization```
- Source the enviroment ``` source oe-init-build-env```
- Add the layer ```bitbake-layers add-layer ../meta-virtualization```
- U would get that error probably
![Screenshot from 2024-08-07 13-55-46](https://github.com/user-attachments/assets/9d3d211c-026c-4a7a-a28d-105f493499b4)
- This error tells u to add this layer also ```bitbake-layers add-layer ../meta-openembedded/meta-filesystems```
- Then do this again ```bitbake-layers add-layer ../meta-virtualization```
![Screenshot from 2024-08-07 14-00-21](https://github.com/user-attachments/assets/72e1892f-b541-44a2-8344-07ae39397906)
- DONE HERE

## Local.conf File
- Add these 2 lines
```
DISTRO_FEATURES:append = " virtualization"
IMAGE_INSTALL:append = " docker-ce"
```
- DISTRO_FEATURES defines the features and capabilities that are included in the distribution. This can include support for various packages, libraries, or system capabilities.
- IMAGE_INSTALL specifies the list of packages that should be installed in the final image. This directly affects the contents of the image being built.
- For space space use this ``` IMAGE_ROOTFS_SIZE = "2194304"```

![Screenshot from 2024-08-07 14-40-48](https://github.com/user-attachments/assets/6014ec38-83d1-4fbf-a54b-b03826361d8d)
- Bingo docker is working now
#### Second let's pull the ignition 
- Go to https://hub.docker.com/r/inductiveautomation/ignition
- Go to Tages and choose the version
- ```docker pull inductiveautomation/ignition:nightly```
- Docker image is already installed it may take a while
 ![Screenshot from 2024-08-15 09-40-21](https://github.com/user-attachments/assets/94e73107-c797-4a6a-95cd-a59d02097762)
- Use the command given by the ignition to run the docker image
```
docker run -d -p 9088:8088 --name ignition-test inductiveautomation/ignition:8.1.42 \
    -n docker-test -a localhost -h 9088 -s 9043
```
- Use ```docker ps``` command to see the docker containers that working now 
![Screenshot from 2024-08-15 09-42-55](https://github.com/user-attachments/assets/818a6d3f-addf-4849-9708-f9c6f88271ba)
- Working now let's try it
- Know the ip of ur device <UR-IP:9088>
- It is working!

![Screenshot from 2024-08-15 09-45-26](https://github.com/user-attachments/assets/566a139a-d57e-42e2-b8fb-ddd837cb9ed5)

## Tips 
- Docker image is very large don't forget to add extra rootfs space in ur image
- Check this variable ```IMAGE_ROOTFS_SIZE```
- https://docs.yoctoproject.org/1.8/ref-manual/ref-manual.html
  ![Screenshot from 2024-08-15 09-50-06](https://github.com/user-attachments/assets/a26cfa4b-9760-4112-8de4-f12f23de3384)
  
- Do not forget to check the requirements of the device to install the ignition
- https://www.docs.inductiveautomation.com/docs/8.1/getting-started/quick-start-guide/download-and-install
![Screenshot from 2024-08-15 09-54-08](https://github.com/user-attachments/assets/831ac7c1-5e7f-49e5-9756-44b2e831051b)

