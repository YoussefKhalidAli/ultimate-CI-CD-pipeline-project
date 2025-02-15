<h2> The documentation of the ultimate CI/CD pipeline of DEPI's DevOps track by team 2 of group ONL2_SWD1_S1</h2>
 
<h5> Group Members</h5>

1. Mahmoud AbdEl-Baset Mohamed
2. Marwan Mobark Omar Mohamed
3. Mosab mahmoud AbdelRahman
4. Yousef Ahmed Samy
5. Youssef Khalid Abdullah Ali


<h3>Phase 1:  Setup Infra</h3>

--------------------------------------------------------------------------------



<h3>Phase 2: Private Git Setup</h3>

In this phase we will set up a repositery to store a copy of the software we will use for the pipeline.

Step 1: Pull https://github.com/jaiswaladi246/Boardgame to your local machine.

Step 2: Create a public repositery on your Github account.

Step 3: Push the downloaded software to your new repositery
 

<h3>Phase 3: CI/CD</h3>

In this phase we will build the ulimate CI/CD pipeline to manage our application.

** Set up jenkins **

Step 1: Install the following plugins:
1. Eclipse Temurin Installer:
2. Pipeline Maven Integration
3. Config File Provider
4. SonarQube Scanner
5. Kubernetes CLI
6. Kubernetes
7. Docker
8. Docker Pipeline Step
9. Maven integration

Step 2: Create the following tools:
1. jdk:
   choose jdk, name it whatever you want(in this case jdk17), and choose jdk17.
2. sonar scanner
   choose SonarQube Scanner, name it(sonar_scanner), and choose version(7.0.2.4839)
3. maven
   choose maven, name it(maven3), and choose version(3.6.1).
4. docker
   choose docker, name it(docker), and choose version(latest)

Step 3: Create the necessary credentials credentials:

1. git token:
   Create a git tooken for your repositery, then create new credentials (username with password) choose your username and add the token as password.
2. sonar token:
   Go to SonarQube server -> administration -> security and add new admin credentials. Copy the token and save it as secret text credentials in jenkins.
3. dockerhub:
   Go to your dockerhub account and create an access token. Create username and password credentials and set username as your dockerhub username and password as the token.
4. k8s:
   Create a svc.yml file in the master k8s cluster:

       apiVersion: v1
       kind: ServiceAccount
       metadata:
         name: jenkins
         namespace: webapps

   Create a role.yml file:

        apiVersion: rbac.authorization.k8s.io/v1
        kind: Role
        metadata:
          name: app-role
          namespace: webapps
        rules:
          - apiGroups:
                - ""
                - apps
                - autoscaling
                - batch
                - extensions
                - policy
                - rbac.authorization.k8s.io
          resources:
            - pods
            - secrets
            - componentstatuses
            - configmaps
            - daemonsets
            - deployments
            - events
            - endpoints
            - horizontalpodautoscalers
            - ingress
            - jobs
            - limitranges
            - namespaces
            - nodes
            - pods
            - persistentvolumes
            - persistentvolumeclaims
            - resourcequotas
            - replicasets
            - replicationcontrollers
            - serviceaccounts
            - services
          verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]

  Create bind.yml file:

      apiVersion: rbac.authorization.k8s.io/v1
      kind: RoleBinding
      metadata:
        name: app-rolebinding
        namespace: webapps 
      roleRef:
        apiGroup: rbac.authorization.k8s.io
        kind: Role
        name: app-role 
      subjects:
      - namespace: webapps 
        kind: ServiceAccount
        name: jenkins

  Create sec.yml file:

    apiVersion: v1
    kind: Secret
    type: kubernetes.io/service-account-token
    metadata:
      name: mysecretname
      annotations:
        kubernetes.io/service-account.name: jenkins

  Apply these files using <i>kubectl apply -f <file></i>.
  Note: apply sec,yml to the webapps namespace.


  Run

      kubectl describe secret mysecretname -n webapps

  and copy the token.


  Finally, add it as secret text credentials.

5. email:
   Go to your google account -> security -> 2FA -> app password and create a new app password, then create a username and password credentials and set username as your email and password as your app password.
   Note: Save your app password for later.

Step 4: Configure jenkins server settings:

1. Scroll to SonarQube installations and add the ip of your SonarQube server and sonar token, name your server(sonar_server).
2. Scroll to extended email notifications, add smtp.google.com to smtp server(if you are using a gmail), choose SSL and set port number to 465, add you email credentials.
3. Scroll to email notification, add smtp.google.com to smtp server(if you are using a gmail), choose SSL and set port number to 465, set username to your email and password to your app password.

Step 5: Add webhook to SonarQube server:

Go to SonarQube -> configuration -> webhooks and add http://<jenkins-server-ip>:8080/sonarqube-webhook/

Step 6: Add Nexus url to pom.xml

Go to pom.xml in the root of the repositery, scroll all the way to the end, and change the ip's in lines 123 and 127 to be the ip of your Nexus server.


** Write the pipleine **

