<h2> The documentation of the ultimate CI/CD pipeline of DEPI's DevOps track by team 2 of group ONL2_SWD1_S1</h2>
 
<h5>Group Members</h5>
<ol>
  <li>Mahmoud AbdEl-Baset Mohamed</li>
  <li>Marwan Mobark Omar Mohamed</li>
  <li>Mosab Mahmoud AbdelRahman</li>
  <li>Yousef Ahmed Samy</li>
  <li>Youssef Khalid Abdullah Ali</li>
</ol>

<h3>Phase 1: Setup Infrastructure</h3>
<hr>

<h3>Phase 2: Private Git Setup</h3>
<p>In this phase, we will set up a repository to store a copy of the software we will use for the pipeline.</p>

<ol>
  <li>Clone the repository from <a href="https://github.com/jaiswaladi246/Boardgame" target="_blank">this link</a> to your local machine.</li>
  <li>Create a public repository on your GitHub account.</li>
  <li>Push the downloaded software to your new repository.</li>
</ol>

<h3>Phase 3: CI/CD</h3>
<p>In this phase, we will build the ultimate CI/CD pipeline to manage our application.</p>

<h4>Set up Jenkins</h4>
<p>Step 1: Install the following plugins:</p>
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

<p>Step 2: Create the following tools:</p>
<ul>
  <li><strong>JDK:</strong> Choose JDK, name it (e.g., jdk17), and choose JDK 17.</li>
  <li><strong>Sonar Scanner:</strong> Name it (sonar_scanner) and choose version 7.0.2.4839.</li>
  <li><strong>Maven:</strong> Name it (maven3) and choose version 3.6.1.</li>
  <li><strong>Docker:</strong> Name it (docker) and choose the latest version.</li>
</ul>

<p>Step 3: Create the necessary credentials:</p>
<ul>
  <li><strong>Git token:</strong> Generate a GitHub token and store it as credentials in Jenkins.</li>
  <li><strong>Sonar token:</strong> Generate a token in SonarQube and save it as secret text credentials.</li>
  <li><strong>DockerHub:</strong> Generate an access token and save it as credentials in Jenkins.</li>
  <li><strong>Kubernetes:</strong> Create and apply the necessary RBAC YAML files.</li>
  <li><strong>Email:</strong> Set up an app password for email notifications.</li>
</ul>

<h4>Step 4: Configure Jenkins Server Settings</h4>
<ul>
  <li>Go to <strong>SonarQube installations</strong>, add the IP of your SonarQube server and sonar token, and name your server (e.g., sonar_server).</li>
  <li>Go to <strong>Extended Email Notifications</strong>, set the SMTP server (e.g., smtp.google.com), choose SSL, and set the port number to 465.</li>
  <li>Go to <strong>Email Notification</strong>, add the SMTP server settings, and set your email credentials.</li>
</ul>

<h4>Step 5: Add Webhook to SonarQube Server</h4>
<p>Navigate to SonarQube → Configuration → Webhooks and add:</p>
<pre>http://your-jenkins-ip:8080/sonarqube-webhook/</pre>

<h4>Step 6: Add Nexus URL to pom.xml</h4>
<p>Go to the <code>pom.xml</code> file in the root of the repository, scroll to the end, and change the IP addresses on lines 123 and 127 to match your Nexus server.</p>

<h4>Write the Pipeline on </h4>

