# Assignment 3 — Production Maintenance Drill (OPS Checklist)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will treat your already deployed React application (on Ubuntu VM with Nginx) as a live production system. You will perform structured operational checks covering network validation, service health, log analysis, resource monitoring, configuration verification, and incident simulation with recovery — mirroring real on-call DevOps responsibilities.

---

# Task 1 — Server Access & Networking Validation

## Goal

Verify that the deployed React application is reachable from the browser and confirm basic network connectivity of the Ubuntu VM.

### Evidence

#### Screenshot 1 — Browser showing the React app with your Full Name visible on the UI

![Assignment 2 Screenshot](./screenshots/week-03-screenshot-10-assignment-02.png)

---

#### Screenshot 2 — Output of `ip a`

![Assignment 2 Screenshot](./screenshots/week-03-screenshot-01-assignment-03.png)

---

#### Screenshot 3 — Output of `sudo ss -tulpen`

![Assignment 2 Screenshot](./screenshots/week-03-screenshot-02-assignment-03.png)

---

#### Screenshot 4 — Output of `sudo ufw status`

![Assignment 2 Screenshot](./screenshots/week-03-screenshot-03-assignment-03.png)

---

### Notes

Answer the following in your own words:

**1. What proves Nginx is listening on 0.0.0.0:80?**

From the `ss -tulpen` command, I can see the port 80 on the `LocalAddress:Port` column with the process `nginx` attached to it

---

**2. What proves SSH is active on port 22?**

From the `ss -tulpen` command, I can see the port 22 on the `LocalAddress:Port` column with the process `sshd` attached to it

---

**3. Did you find any unexpected open ports? Explain briefly.**

Yes i found other unexpected ports like:
- Port 53 - which is the DNS port
- Port 323 - which is the Internet Message Mapping Protocol
- Port 68 - which is the Dynamic Host Configuration Protocol

---

# Task 2 — Service Health & Systemd Validation (Nginx)

## Goal

Verify that Nginx is properly installed, running, enabled at boot, and safely configured.

### Evidence

#### Screenshot 1 — Output of `systemctl status nginx --no-pager`

![Assignment 3 Screenshot](./screenshots/week-03-screenshot-02-assignment-02.png)

---

#### Screenshot 2 — Output of `sudo nginx -t`

![Assignment 2 Screenshot](./screenshots/week-03-screenshot-04-assignment-03.png)

---

#### Screenshot 3 — Output of `sudo ss -lptn '( sport = :80 )'`

![Assignment 2 Screenshot](./screenshots/week-03-screenshot-05-assignment-03.png)

---

### Notes

Answer the following in your own words:

**1. What happens if Nginx fails to restart in production?**

- If Nginx fails to restart in production, your web server immediately stops processing incoming traffic, resulting in site-wide downtime. Users will see 502 Bad Gateway or connection timeout errors, and new requests to the server will drop completely

---

**2. What's your basic rollback plan?**

- Check for errors and fix it.
- Always make sure there's a backup of the working config before modifying it.

---

# Task 3 — Logs & Request Trace

## Goal

Verify real traffic flow and analyze logs to understand system behavior and errors.

### Evidence

#### Screenshot 1 — Output of `sudo tail -n 30 /var/log/nginx/access.log`

![Assignment 2 Screenshot](./screenshots/week-03-screenshot-06-assignment-03.png)

---

#### Screenshot 2 — Output of `sudo tail -n 30 /var/log/nginx/error.log`

![Assignment 2 Screenshot](./screenshots/week-03-screenshot-07-assignment-03.png)

---

#### Screenshot 3 — Output of `sudo journalctl -u nginx --no-pager -n 50`

![Assignment 2 Screenshot](./screenshots/week-03-screenshot-08-assignment-03.png)

---

### Notes

Answer the following in your own words:

**1. Were there any errors in the logs?**

- If yes, mention 1–2 example error lines from the logs and explain what each one means in simple terms.
- If no, explain what it means if the error log is empty or shows no recent errors during your check.

- No, there were no errors. It means all traffic from the internet were routed correctly and all files requested was available (No 400, 500 errors)

---

**2. If there were no errors, what does that indicate about the system?**

- Routing is done correctly and Nginx can find the requested files 

---

**3. Based on the access logs, were your curl requests visible in the log entries? What does that prove about traffic flow?**

- The core Nginx engine is functioning properly
- Logging is working correctly

---

# Task 4 — System Resource Health Check (Capacity Red Flags)

## Goal

Assess server capacity and detect potential performance or failure risks.

### Evidence

#### Screenshot 1 — Output of `uptime`

![Assignment 3 Screenshot](./screenshots/week-03-screenshot-09-assignment-03.png)

---

#### Screenshot 2 — Output of `free -h`

![Assignment 3 Screenshot](./screenshots/week-03-screenshot-10-assignment-03.png)

---

#### Screenshot 3 — Output of `df -h`

![Assignment 3 Screenshot](./screenshots/week-03-screenshot-11-assignment-03.png)

---

#### Screenshot 4 — Output of `sudo du -sh /var/* | sort -h`

![Assignment 3 Screenshot](./screenshots/week-03-screenshot-12-assignment-03.png)

---

### Notes

Answer the following in your own words:

**1. Which resource looks most critical right now? (CPU/load, memory, or disk) Explain why.**

The resource that looks critical for me is the Memory. On the instance, I currently have 200+MB of free memory out of almost 1GB of memory. 

---

**2. What happens if disk becomes 100% full in a production server?**

- SSH becomes difficult
- Processes start to crash because they can't write to disk
- Database corruption may occur

---

# Task 5 — Configuration & Deployment Verification

## Goal

Ensure the correct React build is deployed and Nginx is serving it properly.

### Evidence

#### Screenshot 1 — Output of `ls -lah /var/www/html | head -n 20`

![Assignment 3 Screenshot](./screenshots/week-03-screenshot-13-assignment-03.png)

---

#### Screenshot 2 — Output of `grep -R "Deployed by" -n /var/www/html 2>/dev/null | head`

![Assignment 3 Screenshot](./screenshots/week-03-screenshot-14-assignment-03.png)

---

#### Screenshot 3 — Output of `grep -n "try_files" /etc/nginx/sites-available/default`

![Assignment 3 Screenshot](./screenshots/week-03-screenshot-15-assignment-03.png)

---

### Notes

Answer the following in your own words:

**1. How do you confirm that the correct version of the application is deployed?**

- On the browser, check if the changes made to any file is reflected .

---

# Task 6 — Nginx Configuration Failure Simulation

## Goal

Simulate a real-world Nginx misconfiguration and recover the service safely.

### Evidence

#### Screenshot 1 — Output of `sudo nginx -t` showing the syntax error (broken config)

![Assignment 3 Screenshot](./screenshots/week-03-screenshot-16-assignment-03.png)

---

#### Screenshot 2 — Output of `sudo nginx -t` showing syntax ok (fixed config)

![Assignment 3 Screenshot](./screenshots/week-03-screenshot-17-assignment-03.png)

---

#### Screenshot 3 — Output of `curl -I http://<public-ip>` confirming recovery (200 OK)

![Assignment 3 Screenshot](./screenshots/week-03-screenshot-18-assignment-03.png)

---

### Notes

Answer the following in your own words:

**1. What caused the configuration failure?**

- The root directive in the Nginx config didn't have the closing semicolon.

---

**2. How did you fix the issue?**

- I added the missing semicolon to the root directive.

---

**3. How can you avoid this kind of issue in real production systems?**

- Being thorough while writing the nginx config.

---

# Task 7 — Web Application Failure Simulation

## Goal

Simulate missing deployment content and recover the application safely.

### Evidence

#### Screenshot 1 — Output of `curl -I http://<public-ip>` showing failure (non-200 response)

![Assignment 3 Screenshot](./screenshots/week-03-screenshot-19-assignment-03.png)

---

#### Screenshot 2 — Output of `curl -I http://<public-ip>` confirming recovery (200 OK)

![Assignment 3 Screenshot](./screenshots/week-03-screenshot-18-assignment-03.png)

---

### Notes

Answer the following in your own words:

**1. What caused the application to break in this scenario?**

- The index.html file was missing.

---

**2. How did you fix the issue and restore the application?**

- I added the missing index.html file back to /var/www/html directory.

---

**3. What steps would you take to prevent this kind of issue in real production systems?**

- Make sure the right files are present.
- Make sure the location directive points to the right index file, either index.php or otherwise depending on the tech stack used.

---

# Task 8 — Security & Reliability Review

## Goal

Review and reflect on the security and reliability practices applied during this assignment.

### Security & Reliability Notes

Answer the following in your own words:

**1. Why is SSH key-based authentication more secure than sharing passwords?**

- SSH key-based is more secure because a server can only be accessed by anyone with the .pem file while with passwords, a hacker can guess or crack the passwor.

---

**2. Why should only required ports be open on a production server?**

- Too many unnecessary ports give more room to hackers to access the server. 

---

**3. Why is it important for Nginx to be enabled on boot?**

- So it automatically gets loaded and started when a server reboot occurs.

---

**4. What are the risks of sharing secrets, keys, or credentials publicly?**

- Anyone can get access to them if not secured properly.

---

**5. Why should cloud resources be stopped or terminated when they are no longer needed?**

- To avoid incurring unnecessary cloud bills.

---

# LinkedIn Post (Required)

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

<<<<<<< HEAD
`https://lnkd.in/p/evCezKh5`
=======
`Add your URL here`
>>>>>>> upstream/main

---

#### Screenshot — Published LinkedIn post

![Linkedin Post](./screenshots/LinkedInPosts2.png)

---

# Submission Instructions

- Add all required screenshots in your submission
- Full name must be visible in required screenshots
- Do not expose sensitive information (keys, passwords, account IDs)

---

# Completion Checklist

- [✅] Task 1: Screenshots (browser, ip a, ss -tulpen, ufw status) + Notes answered
- [✅] Task 2: Screenshots (nginx status, nginx -t, ss port 80) + Notes answered
- [✅] Task 3: Screenshots (access log, error log, journalctl) + Notes answered
- [✅] Task 4: Screenshots (uptime, free -h, df -h, du -sh) + Notes answered
- [✅] Task 5: Screenshots (ls html, grep deployed by, grep try_files) + Notes answered
- [✅] Task 6: Screenshots (nginx -t fail, nginx -t pass, curl recovery) + Notes answered
- [✅] Task 7: Screenshots (curl failure, curl recovery) + Notes answered
- [✅] Task 8: Security & Reliability Notes answered
- [✅] LinkedIn post published and URL submitted
- [✅] Full Name visible in all required screenshots
- [✅] No sensitive data exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://pravinmishra.com/dmi  
- 🎓 DevOps for Beginners (Udemy): https://www.udemy.com/course/devops-for-beginners-docker-k8s-cloud-cicd-4-projects/  
- 🎓 Agentic AI DevOps with Claude Code: https://www.udemy.com/course/ultimate-agentic-ai-devops-with-claude-code/  
- 🎓 DevOps with Claude Code: Terraform, EKS, ArgoCD & Helm: https://www.udemy.com/course/devops-with-claude-code-terraform-eks-argocd-helm/  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*