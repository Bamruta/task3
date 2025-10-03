**Task**: Cluster Management - Manage OpenShift cluster lifecycle...	
**Action**: 1. Automated Upgrades: Manage cluster version changes through a GitOps workflow with pre- and post-upgrade checks.
2. Horizontal Scaling: Configure the Cluster Autoscaler to automatically add/remove nodes based on workload demand.		
3. Vertical Scaling: Implement Vertical Pod Autoscaling (VPA) to adjust pod resource requests automatically.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
On **task 1:** you need to update the clusterversion:
command to check: oc get clusterversion
And in overlay/upgrade directory there is 1 file need to change in that.
once it is done click on sync.
and check on cli wherther it is upgrading or not.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
On **task2:** just you have to sync it.
on cli check :
- oc get clusterautoscaler
- oc get machineautoscaler -n openshift-machine-api

and run below command to check it(manually)
Step 1: Verify the namespace
oc get ns

Make sure scale-test exists. If not, create it:
oc create ns scale-test

Step 2: Create a deployment
Here’s an example with the UBI HTTPD image:
oc create deployment apache-test \
  --image=registry.access.redhat.com/ubi8/httpd-24:latest \
  -n scale-test

Step 3: Set resource requests/limits
oc set resources deployment/apache-test \
  --requests=cpu=500m,memory=512Mi \
  --limits=cpu=500m,memory=512Mi \
  -n scale-test

Step 4: Scale replicas to trigger node autoscaling
oc scale deployment/apache-test --replicas=10 -n scale-test

Step 5: Watch pods and nodes
oc get pods -n scale-test -w
oc get nodes -w

if you not see any new  worker node running then increase resouces and scale replicas:
- oc set resources deployment/apache-test --requests=cpu=800m,memory=1Gi --limits=cpu=800m,memory=1Gi -n scale-test
- oc scale deployment/apache-test --replicas=20 -n scale-test

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
On task3: 
