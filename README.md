# smee-ocp

## Setting up Smee

1. Get Smee url from https://smee.io (start a new channel)

    Use url in from-literal (For example url: https://smee.io/somecoolurl)

    ```
    oc create secret generic smee-creds --from-literal=smee-url="https://smee.io/somecoolurl"
    ```

    *Output:* `secret/smee-creds created`

2. Use smee url for webhook in GitHub as well (as payload url)

3. Get listener url from pipeline (event listener under Trigger Templates section in UI) [For example http://el-event-listener-bjlgwa-myproject.myclusterhostname]

4. Use the service for our purposes (for this you can get the service by ending at the namespace of the route and using port 8080 due to how the service and route are created) … in the above case http://el-event-listener-bjlgwa:8080

5. Create a configmap with the listener service url

    ```
    oc create cm pipeline-listener --from-literal=listener-svc="http://el-event-listener-bjlgwa:8080"
    ```

    *Output:* `configmap/pipeline-listener created`

6. Go to git@github.com:siler23/smee-ocp.git for kubectl files to apply in namespace 

7. Clone files locally (with oc configured to correct project) and apply

    ```
    git clone git@github.com:siler23/smee-ocp.git
    cd smee-ocp/
    oc apply -f .
    ```

    *Output 1:* deployment.apps/smee created
    
    *Output 2:* service/smee-ocp created

8. Check Smee pod

    ```
    oc get pods -l app=smee
    ```

    *Output*:
    ```
    NAME                    READY   STATUS    RESTARTS   AGE
    smee-7d955d89b7-g59nj   1/1     Running   0          56s
    ```

9. Proceed as per normal with git update to trigger webhook

10. Your pipeline should start running

11. You can also look at the Smee url for the request from GitHub and in your Smee logs to see the POST to the event listener service

    ```
    oc logs smee-7d955d89b7-mrz5q
    ```

    *Output*:
    ```
    Forwarding https://smee.io/somecoolurl to http://el-event-listener-bjlgwa:8080
    Connected https://smee.io/somecoolurl
    POST http://el-event-listener-bjlgwa:8080/ - 201
    ```