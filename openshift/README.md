# Requirements:  
*  [Install Openshift](https://access.redhat.com/documentation/en-us/openshift_container_platform/4.13/html/installing/index) (and operators listed below)
*  8 Nvidia H100 GPUs
*  3GB locally attached NVMe storage device

# From the Openshift Console navigate to Operator Hub and install these Operators:
*  Node Feature Discovery Operator
*  Nvidia GPU Operator
*  Local Storage Operator
    1)  Create local storage namespace
    "oc new-project openshift-local-storage"
    2) Go to operator hub and search for “local storage operator” , click "install", 
    Select Installation mode “A specific namespace on the cluster” and choose namespace “openshift-local-storage”. 
    Do not change any other settings. Click "subscribe".

# Create Permanent Volumes (PVs) and Permanent Volume Claims (PVCs) for your training data

Local Storage Operator discovers the NVMe device and the other storage devices on your server and creates PVs for each of them.

Now, create a PVC for the PV that was automatically created for your locally attached NVMe drive (which will contain your training data)
IMPORTANT: PVCs are namespaced - PVs are not. Make sure your PVC is in the same namespace where you will run your model. 

create a yaml file for the PVC, and save it as mypvc0.yaml: 
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc0
spec:
  accessModes:
  - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 2980Gi
  storageClassName: local-sc
```

Create the PVC:    
```oc create -f mypvc0.yaml```

# Copy the Training data to the correct directories for each of the models: 
```
/workspace/data/data/     
                    |_ phase1                                   # bert checkpoint to start from (pytorch converted)  
                    |_ phase2    
                    |_undet3d                                  
                     |_unet3dtraining                           # Unet3d training Data
                     |_ phase2    
                     |results    
                     |_ packed_data                              # bert training data   
                     |_hdf5                                      # bert training data 
                           |_ eval                               # evaluation chunks in binary hdf5 format fixed length (not used in training, can delete after data preparation)      
                           |_ eval_varlength                     # evaluation chunks in binary hdf5 format variable length *used for training*    
                           |_ training                           # 500 chunks in binary hdf5 format     
                           |_ training_4320                      #     
                              |_ hdf5_4320_shards_uncompressed   # sharded data in hdf5 format fixed length (not used in training, can delete after data   preparation)    
                              |_ hdf5_4320_shards_varlength      # sharded data in hdf5 format variable length *used for training*    
                              |_ hdf5_4320_shards_varlength_shuffled # shuffled and sharded data in hdf5 format variable length *used for training*    

```

# Run each of the models using the yaml provided under benchmarks: 

for Bert:
oc create -f pod-bert.yaml

for resnet50:
oc create -f pod-resnet50.yaml

for 3d U-Net:
oc create -f pod-unet3d.yaml 






