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
<p><strong>Step 1:</strong> Install the following plugins:</p>
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

<p><strong>Step 2:</strong> Create the following tools:</p>
<ul>
  <li><strong>JDK:</strong> Choose JDK, name it (e.g., jdk17), and select JDK 17.</li>
  <li><strong>Sonar Scanner:</strong> Name it (sonar_scanner) and select version 7.0.2.4839.</li>
  <li><strong>Maven:</strong> Name it (maven3) and select version 3.6.1.</li>
  <li><strong>Docker:</strong> Name it (docker) and choose the latest version.</li>
</ul>

<p><strong>Step 3:</strong> Create the necessary credentials:</p>
<ul>
  <li><strong>Git Token:</strong> Create a GitHub token, then add it as "Username with password" credentials in Jenkins.</li>
  <li><strong>Sonar Token:</strong> In SonarQube, go to <code>Administration → Security</code> to create an admin token. Save it as a "Secret text" credential in Jenkins.</li>
  <li><strong>DockerHub:</strong> Generate an access token in DockerHub and store it as "Username with password" credentials in Jenkins.</li>
  <li><strong>Kubernetes:</strong> Create and apply the necessary RBAC YAML files.</li>
  <li><strong>Email:</strong> Set up an app password for email notifications.</li>
</ul>

<h4>Step 4: Configure Jenkins Server Settings</h4>
<ul>
  <li>Navigate to <strong>SonarQube installations</strong>, add the IP of your SonarQube server and Sonar token, and name the server (e.g., sonar_server).</li>
  <li>In <strong>Extended Email Notifications</strong>, set the SMTP server (e.g., <code>smtp.google.com</code>), enable SSL, and set the port to 465.</li>
  <li>In <strong>Email Notification</strong>, configure SMTP settings and add your email credentials.</li>
</ul>

<h4>Step 5: Add Webhook to SonarQube Server</h4>
<p>Navigate to <code>SonarQube → Configuration → Webhooks</code> and add:</p>
<pre>http://your-jenkins-ip:8080/sonarqube-webhook/</pre>

<h4>Step 6: Add Nexus URL to pom.xml</h4>
<p>Go to the <code>pom.xml</code> file in the root of the repository, scroll to the end, and change the IP addresses on lines 123 and 127 to match your Nexus server.</p>

<h4>Step 7: Kubernetes Configuration</h4>
<p>Create the following Kubernetes YAML files:</p>

<pre>
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins
  namespace: webapps
</pre>

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

<pre>
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: mysecretname
  annotations:
    kubernetes.io/service-account.name: jenkins
</pre>

<p>Apply these files using:</p>
<pre>kubectl apply -f .</pre>

<p>After applying <code>sec.yml</code> to the <code>webapps</code> namespace, retrieve the token with:</p>
<pre>kubectl describe secret mysecretname -n webapps</pre>

<p>Copy the token and save it as a "Secret text" credential in Jenkins.</p>

<h4>Step 8: Configure Email Notifications</h4>
<p>To enable email notifications in Jenkins:</p>
<ul>
  <li>Go to your Google account → Security → Enable 2FA → Create an App Password.</li>
  <li>Save the generated app password.</li>
  <li>In Jenkins, create a "Username with password" credential using your email and app password.</li>
</ul>

<h4>Step 9: Write the Jenkins Pipeline</h4>
<p>Create a Jenkins pipeline file and push it to your GitHub repository.</p>
