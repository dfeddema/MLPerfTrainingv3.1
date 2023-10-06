# Requirements:  
*  [Install Openshift](https://access.redhat.com/documentation/en-us/openshift_container_platform/4.13/html/installing/index) (and operators listed below)
*  8 Nvidia H100 GPUs
*  7TB locally attached NVME

# From the Openshift Console navigate to Operator Hub and install these Operators:
*  Node Feature Discovery Operator
*  Nvidia GPU Operator
*  Local Storage Operator
  1)  Create local storage namespace
      "oc new-project openshift-local-storage"
  2) Go to operator hub and search for “local storage” , click "install", 
     Select Installation mode “A specific namespace on the cluster” and choose namespace “openshift-local-storage”. 
     Do not change any other settings. Click "subscribe".

# Create PVs and PVCs for your training data

Local Storage Operator discovers the NVMe device and the other storage devices on your server and creates PVs for each of them.

Create a PVC for the PV that was automatically created for your locally attached NVMe drive (which will contain your training data)
IMPORTANT: PVCs are namespaced - PVs are not. Make sure your PVC is in the same namespace where you will run your model. 


# Copy the Training data to the correct directories for each of the models: 

bertBackup/  

checkpoint_dir/  

hdf5/  

packed_data/  

per_seqlen/  

per_seqlen_parts/  

phase1/  

phase2/  

results/  

undet3d/  

unet3dtraining/  


# Run each of the models using the yaml provided under benchmarks: 

for Bert:
oc create -f pod-bert.yaml

for resnet50:
oc create -f pod-resnet50.yaml

for 3d U-Net:
oc create -f pod-unet3d.yaml 






