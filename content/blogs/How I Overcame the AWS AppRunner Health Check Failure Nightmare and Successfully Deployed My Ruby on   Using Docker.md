### **The Start of My AWS AppRunner Deployment Nightmare**

  

Have you ever faced a deployment problem so persistent that it seemed impossible to solve? That’s exactly what I encountered when I tried deploying my Ruby on Rails app to **AWS AppRunner**. Despite having some experience with Docker, I quickly realized that deploying to **AWS AppRunner** was a different challenge altogether.

  

Initially, I tried using a **boilerplate** created by my senior colleague, who was working on a **Linux** system.(i noticed about the system later) The app ran fine there, but when I tried deploying it on my **MacOS** machine, the deployment failed, and the **health check** didn’t pass.

  

Frustrated, I decided to build the deployment from scratch rather than relying on the pre-existing **Docker build**. The first step was to create my **Dockerfile** and deployment configuration. Locally, everything seemed to work fine, but when I pushed it to **AWS ECR** and tried deploying on **AppRunner**, the **health check** failed again.

  

### **Why Did the Health Check Fail on AWS AppRunner?**
Have you ever been stuck on a deployment issue and felt like you were going in circles? That’s what I felt as I tried repeatedly to deploy my Ruby on Rails app on **AWS AppRunner**, only to have the **health check** fail each time.
  ![Frustrated image](content/blogs/images/frutationand_stuck.png)

I was determined to figure out what went wrong. So, I spent 2 days troubleshooting the **Docker build** and **AWS AppRunner** configurations. I tried deploying directly from the **Git repository** using **Ruby 3.1 build time**, so I could access the build logs, but encountered a PostgreSQL version incompatibility.

  

Still, the **health check failure** persisted, and the **AWS AppRunner** logs didn’t provide the insights I needed. This left me feeling stuck.

  

### **The Breakthrough: Platform Compatibility and Docker Buildx**

  

As I continued to research, I realized there could be a **platform compatibility** issue. My senior’s **boilerplate** worked perfectly on Linux, but its me not on MacOS. I was working with **MacOS**, but the app was likely optimized for **Linux**.

  

That’s when I discovered the **buildx command** in Docker. **Docker buildx** is a powerful tool that allows Docker to build images for multiple platforms, such as **linux/amd64**. This was the key to resolving the **platform compatibility** issues I was facing with **AWS AppRunner**.

  

I wasn’t out of the woods yet, though. After solving this problem, I ran into another issue: a missing **masterkey**. After tweaking the configuration, the issue was resolved, but there were still more challenges ahead.

  

### **Final Hurdles: RDS Permissions and Git Workflow**

  

After resolving the **masterkey** issue, I faced another roadblock: the **RDS DB** wasn’t accepting inbound and outbound permissions, which prevented my app from connecting to the database. After some adjustments, I got the **RDS** connection working.

  

But wait—there was still one more hurdle. I needed to run the **Git workflow**, and that’s when the **Linux environments** used in **Git runners** came into play. Finally, the app was ready for deployment.

  

### **The Solution: How I Fixed the Health Check and Deployed to AWS AppRunner**

  

Finally, after many sleepless nights, I clicked the “Deploy” button, and to my amazement, the deployment succeeded! The **health check** passed, and my Ruby on Rails app was deployed on **AWS AppRunner**.

  

So, what was the final fix? It was surprisingly simple: I replaced a single line in my **Docker build** command.

  

#### **Before:**

```bash

docker build -t $APP_NAME -f Dockerfile.apprunner .

```

  

#### **After (the fix):**

```bash

docker buildx build --platform=linux/amd64

```

  

Yes, just a **6-line fix**! Once I added the `--platform=linux/amd64` option to my **Docker build** command, everything fell into place, and the app was deployed successfully on **AWS AppRunner**.

  

### **A Well-Deserved Sense of Accomplishment**

  

After hours of troubleshooting and **health check failures**, I finally deployed my Ruby on Rails app on **AWS AppRunner**. The feeling of overcoming this challenge was incredible. I shared my success with the team and celebrated the victory. Sometimes, the solution to a complex problem is simpler than you think.

  

Have you ever had a **health check failure** or encountered platform-specific deployment issues like I did with **AWS AppRunner**? What was your breakthrough moment? Share your experiences in the comments—I’d love to hear your deployment stories!

  

### **Need Help with AWS AppRunner Deployment?**

  

If you're struggling with deploying your app to **AWS AppRunner** or dealing with **health check failures**, don’t worry—I’ve been there! Feel free to reach out to me through my socials, and I’ll help you troubleshoot the deployment challenges. Let’s work together and solve the problem!
