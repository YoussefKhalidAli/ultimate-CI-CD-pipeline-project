<h2> The documentation of the ultimate CI/CD pipeline of DEPI's DevOps track by team 2 of group ONL2_SWD1_S1</h2>

<h5>Group Members</h5>
<ol>
  <li>Mahmoud AbdEl-Baset Mohamed</li>
  <li>Marwan Mobark Omar Mohamed</li>
  <li>Mosab Mahmoud AbdelRahman</li>
  <li>Yousef Ahmed Samy</li>
  <li>Youssef Khalid Abdullah Ali</li>
</ol>

<h3>Phase 1: Setup Infra</h3>
<hr>

<h3>Phase 2: Private Git Setup</h3>
<p>In this phase, we will set up a repository to store a copy of the software we will use for the pipeline.</p>

<ol>
  <li>Pull <a href="https://github.com/jaiswaladi246/Boardgame" target="_blank">this repository</a> to your local machine.</li>
  <li>Create a public repository on your GitHub account.</li>
  <li>Push the downloaded software to your new repository.</li>
</ol>

<h3>Phase 3: CI/CD</h3>
<p>In this phase, we will build the ultimate CI/CD pipeline to manage our application.</p>

<h4>Set up Jenkins</h4>
<h5><strong>Step 1:</strong> Install the following plugins:</h5>
<ul>
  <li>Eclipse Temurin Installer</li>
  <li>Pipeline Maven Integration</li>
  <li>Config File Provider</li>
  <li>SonarQube Scanner</li>
  <li>Kubernetes CLI</li>
  <li>Kubernetes</li>
  <li>Docker</li>
  <li>Docker Pipeline</li>
  <li>Maven Integration</li>
</ul>

<h5><strong>Step 2:</strong> Create the following tools:</h5>
<ul>
  <li><strong>JDK:</strong> Choose JDK, name it (e.g., jdk17), and select JDK 17.</li>
  <li><strong>Sonar Scanner:</strong> Name it (e.g, sonar_scanner) and select version 7.0.2.4839.</li>
  <li><strong>Maven:</strong> Name it (e.g, maven3) and select version 3.6.1.</li>
  <li><strong>Docker:</strong> Name it (e.g, docker) and choose the latest version.</li>
</ul>

<h5><strong>Step 3:</strong> Create the necessary credentials:</h5>
<ul>
  <li><strong>Git Token:</strong> Create a GitHub token, then add it as "Username with password", choose a username and set the password as the token.</li>
  <li><strong>Sonar Token:</strong> In SonarQube, go to <code>Administration → Security</code> to create an admin token. Save it as a "Secret text" credential in Jenkins.</li>
  <li><strong>DockerHub:</strong> Generate an access token in DockerHub and store it as "Username with password", choose a username and set the password as the token.</li>
  <li><strong>Kubernetes:</strong> Create and apply the necessary the following files.
    svc.yml
  <pre>
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins
  namespace: webapps
</pre>
role.yml
<pre>
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: webapps
rules:
  - apiGroups: ["", "apps", "autoscaling", "batch", "extensions", "policy", "rbac.authorization.k8s.io"]
    resources: ["pods", "secrets", "componentstatuses", "configmaps", "daemonsets", "deployments", "events", "endpoints", "horizontalpodautoscalers", "ingress", "jobs", "limitranges", "namespaces", "nodes", "persistentvolumes", "persistentvolumeclaims", "resourcequotas", "replicasets", "replicationcontrollers", "serviceaccounts", "services"]
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
</pre>
bind.yml
<pre>
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
</pre>
sec.yml
<pre>
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: mysecretname
  annotations:
    kubernetes.io/service-account.name: jenkins
</pre>

<p>Apply the files using:</p>
<pre>kubectl apply -f .</pre>
<b>Note: apply the sec.yml file to the webapps namespace</b> 
<p>copy the token with:</p>
<pre>kubectl describe secret mysecretname -n webapps</pre>

<p>Save it as a "Secret text" credential in Jenkins.</p>
  </li>
  <li><strong>Email:</strong> Set up an app password for email notifications.
<ul>
  <li>Go to your Google account → Security → Enable 2FA → Create an App Password.</li>
  <li>Save the generated app password.</li>
  <li>In Jenkins, create a "Username with password" credential using your email and app password.
  <b>Note: save the app password for later</b>
  </li>
</ul>
  </li>
</ul>

<h5>Step 4: Configure Jenkins Server Settings</h5>
<ul>
  <li>Navigate to <strong>SonarQube installations</strong>, add the IP of your SonarQube server and Sonar token, and name the server (e.g., sonar_server).</li>
  <li>In <strong>Extended Email Notifications</strong>, set the SMTP server (smtp.google.com if you are using gmail), enable SSL, and set the port to 465.</li>
  <li>In <strong>Email Notification</strong>, configure SMTP settings and add your email as your username and app password as password</li>
</ul>

<h5>Step 5: Add Webhook to SonarQube Server</h5>
<p>Navigate to <code>SonarQube → Configuration → Webhooks</code> and add:</p>
<pre>http://<your-jenkins-ip>:8080/sonarqube-webhook/</pre>

<h5>Step 6: Add Nexus URL to pom.xml</h5>
<p>Go to the <code>pom.xml</code> file in the root of the repository, scroll to the end, and change the IP addresses on lines 123 and 127 to match your Nexus server.</p>

<h5>Step 7: Configure Email Notifications</h5>

<ul>
  <li>
    Scroll to extended email notifications, add smtp.google.com to smtp server(if you are using a gmail), choose SSL and set port number to 465, add you email credentials.
  </li>
  <li>
    Scroll to email notification, add smtp.google.com to smtp server(if you are using a gmail), choose SSL and set port number to 465, set username to your email and password to your app password.
  </li>
</ul>

<h4>Write the pipeline</h4>
