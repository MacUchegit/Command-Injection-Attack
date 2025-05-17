![Untitled](https://github.com/user-attachments/assets/540d6bd0-c31e-4a7a-992a-992c5f6f63b5)
----

# 🎯 Investigatimg a Real Command Injection Attack
----

## **Introduction**  

Security isn't just about tools; it's about the stories they tell. Recently, I completed a thrilling investigation on a suspicious event flagged by Let's Defend's SIEM platform — and today, I’m walking you through exactly how it went down.  

### 🛡️ First, What's a Command Injection Anyway?  

Imagine you’re talking to a robot assistant. You say, "Play music," and it does. But what if a stranger whispers secret, harmful commands — like "Open the safe" — and the robot listens? That’s a command injection attack: when attackers trick a system into running their sneaky commands instead of the safe ones it's supposed to obey.  

For the tech folks: it usually happens when user inputs aren't properly sanitized, letting attackers slip OS-level commands into web applications.  

### 🔥 The Incident Overview: A Red Flag Appears  

At 4:12 AM on February 28th, an alert fired off:  

**Rule:** SOC168 - Whoami Command Detected in Request Body  
**Hostname:** WebServer1004  
**Source IP:** 61.177.172.87  
**Destination:** 172.16.17.16  

**Suspicious Clue?** The attacker sent a POST request containing the command `whoami` — a classic hacker move to figure out who they’ve broken into.  

![1745676726238](https://github.com/user-attachments/assets/6658b914-f9e7-4e08-be9c-467e35cc07d2)

### 🕵️ Step 1: Understand Why the Alarm Went Off  

Before rushing into action, it’s smart to pause and understand:  
- Why did this rule trigger?  
- What is it trying to protect us from?  

Looking at the rule name, "Whoami Command Detected," gave an immediate clue: someone’s trying to run system-level commands through our web server!  

### 📜 Step 2: Digging Through the Logs  

Using the source IP address, I filtered the logs — and boom! The attacker wasn't just whispering — they were screaming:  
- `?c=whoami`  
- `?c=ls`  
- `?c=uname`  
- `?c=cat /etc/passwd`  
- `?c=cat /etc/shadow`  

**Here are some examples:**  

![1745677628340](https://github.com/user-attachments/assets/53057434-874b-4b86-974c-225991d2a8d0)

![1745677659484](https://github.com/user-attachments/assets/31ea6bd6-5b10-4492-8c7e-d9d534c6ecbe)

![1745677714262](https://github.com/user-attachments/assets/f20b00a4-0f16-4f0b-82b4-baaa5ecb122e)

![1745677752677](https://github.com/user-attachments/assets/0bbbab0e-4dce-4635-8245-2ec45131b7c9)

Commands like `cat /etc/passwd` (which reads user account details) screamed **COMMAND INJECTION!** They weren't just snooping; they were trying to take over.  

### ⚔️ Step 3: Was This Traffic Really Malicious?  

![1745678404556](https://github.com/user-attachments/assets/9affee67-8635-4597-ab8d-cbd499ea0e0c)

Absolutely. These weren’t normal web traffic behaviors; they were classic attacker moves aiming to explore and exploit the server.  

### 🔎 Step 4: Could This Be a Drill? 

![1745678552205](https://github.com/user-attachments/assets/2b1bfbe9-922b-45f0-8b6b-d115efc78d9b)

Maybe this was just a routine cybersecurity test? To find out, I navigated to the Email Security page and ran a filtered search using keywords like "command" and "whoami."  

Interestingly, I did find two emails containing the word "command," but after reviewing them, there was no mention of any planned drills or penetration tests.  

![1745678886701](https://github.com/user-attachments/assets/0a7b1740-aa6d-4ddd-b63d-9eede9e33624)

![1745678912103](https://github.com/user-attachments/assets/ab817829-fe62-446f-a49e-fd21b9e7e663)

![1745678934279](https://github.com/user-attachments/assets/ba06f7d7-f23c-4023-8faf-c14e2d1917e5)

**✅ Conclusion:** This was not a scheduled exercise — it was a real attack.  

### 🌍 Step 5: Traffic Direction Matters  

![1745683554112](https://github.com/user-attachments/assets/7e27b575-02c3-49eb-86b1-04a3c2d39fd4)

To understand if this was an insider mistake or an external attack, I checked the traffic flow:  
- **Source IP Address:** 61.177.172.87 → (This is a public IP address, coming from the Internet.)  
- **Destination IP Address:** 172.16.17.16 → (This is a private internal IP, part of our company network.)  

**🛡️ Reason:** Since the source IP is external and the destination IP is internal, it clearly shows an outsider trying to reach inside our network, which is a major security red flag.  

**Quick Visual:**  
`[Internet] (61.177.172.87) ➡️ [Company Network] (172.16.17.16)`  

**✅ Conclusion:** The attack came from the outside world into our internal network — classic case of external threat activity.  

### 🚦 Step 6: Was the Attack Successful?  

![1745683576115](https://github.com/user-attachments/assets/2ba3201a-1b79-4a9a-8d48-e3100f0f36d9)

Looking closer, I noticed HTTP responses with `200 OK` and large data sizes. This suggests that the attack probably succeeded in extracting data from the server.  

![1745683594707](https://github.com/user-attachments/assets/3dfd223f-f020-41a4-95c2-2b3470555203)

### 🛡️ Step 7: Containment Time  

No time to waste. I quickly navigated to Endpoint Security, filtered for our server's IP, and slammed the "Request Containment" button to isolate the device.  

**✋ Containment is key to stop the hacker from spreading deeper into the network!**  

![1745683627702](https://github.com/user-attachments/assets/e6ebb8df-03f8-4a60-8db6-cd23c2eda1f8)

### 📂 Step 8: Documenting Artifacts  

I logged the malicious IP address `61.177.172.87`. Recording artifacts is crucial because it strengthens threat intelligence and helps prevent future attacks.  

![1745684348303](https://github.com/user-attachments/assets/9915afc5-e099-420f-a8c0-998cab639376)

### 🚀 Step 9: Tier 2 Escalation  

![1745684432785](https://github.com/user-attachments/assets/a3256f76-3eb1-4041-a4f4-3e53a348f67c)

Because the attack was successful, this case demanded escalation to Tier 2 experts. Why?  
- To perform deep forensics  
- Patch vulnerabilities  
- Monitor for any backdoors the attacker might have left.  

### 📝 Step 10: Final Comment and Closing the Alert  

In my comment field, I summarized my analysis.  
Finally, I closed the case as a **True Positive** — because, without a doubt, this was a real-world attack.  

![1745684469892](https://github.com/user-attachments/assets/93df9006-1542-42ac-8ac2-367cf8e6c182)

### 🎯 Conclusion  

This investigation wasn't just about clicking buttons; it was about thinking like a detective, understanding clues, and taking action fast. Command injection attacks can be deadly if left unchecked — but with the right skills, tools, and mindset, they can be caught and crushed before causing major damage.  
