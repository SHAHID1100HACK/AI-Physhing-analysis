# INCIDENT RESPONSE LAB 

### 🛠️ Tools & Technologies Used
* **Security Information & Event Management (SIEM):** Used Splunk to query logs and track the spread of the phishing email across the organization.
* **Threat Intelligence:** Used VirusTotal to analyze the reputation of the malicious URL and domain age.
* **Email Security:** Simulated email header analysis to identify typosquatting (`c0mpany-hr.net`) and spoofing.
* **Whois:** Used to verify domain registration details and identify "freshly" created domains (< 48 hours).

### 🧠 Lessons Learned
* **Typosquatting Awareness:** Learned to look for subtle character replacements (like `0` for `o`) in sender addresses, which is a common technique to bypass quick visual inspections.
* **Containment Strategy:** Understood that resetting the user's password is a critical immediate step, even if the user claims they didn't click, to prevent potential lateral movement.
* **Scope Assessment:** Realized that finding one phishing email is rarely the end; you must always pivot to search the entire environment for the same indicators (Sender/Subject) to find other victims.





# Incident Description:

On December 30, 2025, at 09:30 AM, the Security Operations Center (SOC) received a user-reported phishing attempt via the 'Report Phishing' button. The reporting user, John Doe, flagged an email claiming to be from Human Resources regarding a payroll discrepancy. Preliminary visual inspection revealed typosquatting in the sender address and a suspicious external link (.xyz domain) requesting credential input.




## 📝 Incident Report: IR-2025-12-30

Incident Type: Phishing / Credential Harvesting Severity: Medium Status: CLOSED Analyst: [Your Name]
1. Executive Summary

A user (John Doe) reported a suspicious email regarding "Year End Bonus Verification." Analysis confirmed it was a credential harvesting attempt using a typosquatted domain. The threat was contained, and the email campaign was purged from 14 other mailboxes.
2. Artifact Analysis (The Evidence)

    Sender: alerts@c0mpany-hr.net (Spoofed "company" with a zero)

    Subject: "ACTION REQUIRED: Year End Bonus Verification"

    Malicious URL: http://acme-corp-portal-secure-logon.xyz/auth.php

    Intelligence: VirusTotal flagged the domain as malicious (18/90 vendors); domain age < 2 days.

3. Actions Taken (Containment & Eradication)

    [Network] Blocked access to the malicious domain *.xyz on the corporate firewall.

    [Identity] Forced password reset for user John Doe.

    [Email Security] Performed SIEM search for sender alerts@c0mpany-hr.net.

    [Cleanup] Identified and hard-deleted the phishing email from 14 target mailboxes (Finance/Sales depts).

4. Conclusion

No successful compromise detected. The attack vector has been neutralized.
