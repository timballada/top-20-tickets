# 20 Common Tier-1 IT Support Issues (Windows, Office 365, AD, UniFi)

---

# Table of Contents

- [Forgotten Password or Account Lockout](#forgotten-password-or-account-lockout)
- [Unable to Login to Windows (Non-Password Issues)](#unable-to-login-to-windows-non-password-issues)
- [Outlook Email Sending or Receiving Problems (Office 365)](#outlook-email-sending-or-receiving-problems-office-365)
- [Microsoft Teams / Skype Issues (Meetings and Audio / Video)](#microsoft-teams-'/'-skype-issues-meetings-and-audio-'/'-video)

---

### Forgotten Password or Account Lockout

**Description:** Users often forget their Windows/AD passwords or get locked out after too many failed attempts. They cannot log into their computer or Office 365 account until the password is reset or the account unlocked. This is one of the most frequent help desk requests.

**Initial Questions:**

* Are you seeing a specific message (e.g., “account locked” or “incorrect password”)?
* Did you recently change your password, or has it expired?
* Are you working on-site (connected to the domain network) or remotely (off VPN)?
* Have you tried using any self-service password reset portal (if available)?

**Troubleshooting Steps:**

1. **Verify User Identity:** Follow your company’s security policy to confirm the caller’s identity before proceeding with a password reset.
2. **Check Account Status:** In Active Directory Users and Computers (ADUC), find the user’s account. See if it’s locked out or if the password is expired. Unlock the account if it is locked.
3. **Reset Password:** If the user forgot the password, use ADUC (or Office 365 admin portal for cloud accounts) to set a temporary password. Ensure “User must change password at next login” is checked so they can set a new one.
4. **Advise the User:** Provide the new temporary password and have them try logging in. Remind them about case sensitivity (check Caps Lock) and proper domain username format if applicable.
5. **Remote User Consideration:** If the user is remote and not currently on VPN, explain that the new AD password may not immediately sync to their laptop if they aren’t connected. They might need to use their old password to log into the laptop offline, then connect to VPN with the new password so the cached credentials update. (Alternatively, they can log into webmail/Office 365 with the new password until they can VPN in.)
6. **Confirmation:** Stay on the line while the user attempts to log in with the new credentials. Ensure they can access their email and other resources with the updated password.

**Common Resolutions & Next Steps:**

* **Password Reset/Unlock Success:** The user gains access with the new password. Instruct them to memorize the new password (or use a password manager) and note the company’s password policy (length, complexity, expiration). This resolves the ticket in most cases.
* **Account Still Unaccessible:** If the user still cannot log in (e.g., password reset not taking effect, or they’re using the correct new password but it fails), double-check for typos and ensure their account isn’t **disabled** or under any restriction. If everything looks normal but login fails, **escalate to Tier 2** – there could be an underlying AD replication issue or a deeper account problem.
* **Frequent Lockouts:** If the account keeps locking after reset, there may be a stuck service or device with an old password (e.g. email on phone). Advise the user to update passwords on all devices. For persistent lockout problems, escalate for further AD troubleshooting (log review) to find the source of bad login attempts.

---

### Unable to Login to Windows (Non-Password Issues)

**Description:** The user has the correct password but still cannot log into their Windows system. They might encounter errors like “The user profile service failed the logon” (profile corruption) or “Trust relationship between this workstation and the primary domain failed” (domain trust issue). In a corporate domain environment, these login errors are a common support issue beyond just forgotten passwords.

**Initial Questions:**

* What error message or behavior do you see when you try to log in? (E.g., profile error, domain error, or it just loops back to the login screen.)
* Has this computer been off the network for an extended time, or was it recently changed (new computer, renamed, or re-imaged)?
* Can you log in with *any* account on this PC (if you have a local admin or another domain account to test)?
* Did anything notable happen before this started (power outage, Windows update, etc.)?

**Troubleshooting Steps:**

1. **Determine Error Type:** If it’s a “user profile” issue (Windows loads a temporary profile or gives a profile error), it suggests the user’s profile is corrupted. If it’s a “trust relationship” or domain not available error, it suggests the computer’s domain connection is broken or the user is off network.
2. **For Profile Issues:** Guide the user (or use remote tools via admin account) to log in as a different user or in Safe Mode. If possible, back up the user’s important files from their profile folder. You may fix the issue by removing or renaming the corrupted profile: open Registry (regedit) and rename the user’s profile key under `HKLM\Software\Microsoft\Windows NT\CurrentVersion\ProfileList` (this is advanced – if you’re a new tech and not comfortable, escalate). On next login, Windows will create a new profile.
3. **For Domain Trust Issues:** This usually requires the PC to be rejoined to the domain. If the user is seeing “trust relationship failed,” a Tier 1 can attempt to resolve by: logging in with a local administrator account (or using cached credentials of a domain admin, if available) and then rejoining the domain (remove from domain to a workgroup, then back to domain). This requires network connectivity to a domain controller. If you lack rights or it’s complex, escalate to desktop support.
4. **Network Connectivity Check:** Sometimes login issues occur because the computer cannot reach a domain controller (e.g., user is on VPN and it’s not connected at login). Ask the user if they are connected to the corporate network (wired or Wi-Fi). For remote users, ensure they know how to use the “login with cached credentials” (if they *have* logged in on this laptop before, they should be able to log in without network using last known creds). If their cached credentials got out of sync with a changed password, you may need to help them connect via VPN after a local login (perhaps using an old password) or use a local account.
5. **Account Status:** Double-check that the user’s AD account is not locked or disabled (just in case the issue is actually credentials – overlap with Issue #1).
6. **Attempt a Workaround:** If the user has a local account or you can enable the built-in Administrator (via recovery environment), they might log in and continue work while the domain issue is fixed. But typically, at this point, you will gather information and proceed to next step.
7. **Escalate if Unresolved:** If these steps are beyond your access level or didn’t resolve the login problem, escalate to Tier 2/Desktop team. Provide them the error details and steps taken. They may need to do on-site repair (for example, if Windows is heavily corrupted, an OS repair or re-image might be needed).

**Common Resolutions & Next Steps:**

* **Profile Fix Applied:** For a corrupted user profile, creating a fresh profile usually resolves the login issue. The user can then sign in successfully, though they may need to reconfigure some personal settings. Any files from the old profile can be restored from the backup. This fix addresses the immediate problem and the ticket can be closed after verification.
* **Domain Trust Restored:** Rejoining the PC to the domain (or fixing network issues so it can contact the domain) resolves “trust relationship” errors. The user can log in normally with domain credentials. Document that the machine account was reset.
* **Persistent or Complex Issues:** If the user still cannot log in, it could indicate deeper issues (like Windows OS corruption or group policy issues). In such cases, Tier 2 might use a tool to repair Windows, or re-image the PC if needed. Always ensure the user’s data is safe (backed up) before such drastic steps. Keep the user informed and, if it’s an extended fix, provide a loaner machine if possible.

---

### Outlook Email Sending or Receiving Problems (Office 365)

**Description:** Email issues, such as Outlook not sending or receiving messages, are high-priority because they disrupt business communication. Users may report Outlook showing “Disconnected” or not updating inbox, or that emails are stuck in the Outbox. Such email woes can severely impact productivity. Common causes include Outlook client issues, network problems, or service outages in Office 365.

**Initial Questions:**

* Are you using the Outlook desktop app, and if so, is there any error (e.g., “Trying to connect” or prompt for a password)? Or is this an issue with Outlook Web (OWA) as well?
* Can you reach other internet services? (This helps determine if it’s a connectivity issue.)
* Do you know if others in your office are having similar email issues, or is it just your account?
* When was the last time it worked, and have you changed your password or made any changes since then?

**Troubleshooting Steps:**

1. **Check Internet Connection First:** Ensure the user’s PC is online. Have them open a web browser and verify they can reach an external site (like news or company homepage). Without internet, Outlook (especially Office 365) won’t connect. If there’s no internet, resolve that first (see Network issues).
2. **Test Web Access:** Ask the user to try Outlook Web App via Office 365 portal. If webmail works fine (they can send/receive there), then the issue is likely with the local Outlook application. If webmail also doesn’t work or the account can’t sign in, it could be an account issue or broader service problem.
3. **Credential Prompt:** If Outlook is prompting for a password repeatedly, have the user re-enter their Office 365 password. A recent password change not yet updated in Outlook can cause this. After entering the correct password, see if it connects.
4. **Restart Outlook/Computer:** Sometimes the simplest fix is to close Outlook and reopen it, or reboot the computer. This can clear minor glitches or apply pending updates. It’s quick and often resolves transient connectivity issues.
5. **Check for Errors:** If Outlook shows an error like “Work Offline” or “Disconnected,” ensure it’s not accidentally in offline mode (look at the Outlook status bar for “Working Offline”). If it is, go to **Send/Receive** tab and toggle **Work Offline** off.
6. **Update and Profile Check:** Confirm that Office is up to date. Outdated Outlook versions or a corrupted mail profile are common culprits. If the issue persists, create a new Outlook profile: (Control Panel > Mail > Show Profiles > Add) and configure it anew for the user’s account. This often fixes issues where the old profile was corrupted.
7. **Service Outage Check:** If multiple users report email issues, check the Office 365 Service Health Dashboard (if you have admin access) for any Exchange Online outage. You can also check with a colleague or your IT team. If there is a known outage, inform the user that Microsoft is working on it.
8. **Support Tools:** If available, run Microsoft’s Support and Recovery Assistant (Sara) for Outlook, which can automatically diagnose common Outlook connectivity problems.
9. **Mailflow Specific:** If the user can receive but not send (or vice versa), check mailbox quotas and rules. An extremely full mailbox can prevent sending. Also, ask if they receive any bounce-back emails or error codes when sending. That can guide next steps (e.g., specific SMTP errors).
10. **Escalation:** If none of the above resolves the issue and it’s isolated to this user’s Outlook (especially if webmail works), escalate to Tier 2. They may need to dig into advanced Outlook settings, OST file issues, or re-install Office. If it’s a broader email problem (many users or entire office), escalate to the messaging team or Microsoft support as appropriate while keeping users informed.

**Common Resolutions & Next Steps:**

* **Local Fixes:** Many Outlook issues are resolved by restarting Outlook or re-creating the mail profile. Also, installing the latest Office updates can patch known bugs. Once the user can send/receive again, have them confirm by sending a test email. Document what fixed it (e.g., “Created new Outlook profile”) for future reference.
* **Password or Auth Issues:** If the user recently changed their AD password and forgot to update it in Outlook, simply re-authenticating resolves the issue. In Office 365, once credentials are correct and perhaps MFA approved, Outlook should connect.
* **Service Outage:** If it was a Microsoft 365 service issue, the resolution comes from Microsoft’s side. The help desk’s role is to keep the user informed and provide workarounds (like OWA or mobile email) in the meantime. Once service is restored, confirm the user’s Outlook is back online.
* **Escalation:** Should the problem persist (for example, Outlook keeps disconnecting due to a deeper network or mailbox issue), Tier 2/3 might need to analyze network traffic or server-side logs. They might also consider migrating the mailbox or other advanced fixes. Ensure the user knows that the issue is acknowledged and being worked on, even if Tier 1 cannot fix it immediately.

---

### Microsoft Teams '/' Skype Issues (Meetings and Audio '/' Video)

**Description:** Collaboration tools like **Microsoft Teams** (or Skype for Business in some environments) are essential for communication. Issues such as failing to join meetings, broken audio or video, or chat messages not sending are common. For example, a user might complain that their microphone isn’t working in Teams, or they can’t see other participants. These issues can disrupt meetings and productivity, so quick troubleshooting is important. (Teams issues can range from user device settings to broader network or service problems.)

**Initial Questions:**

* Is the problem with **audio, video, or joining the meeting** itself? (This identifies whether it’s a device issue or connectivity.)
* Are you getting any error messages in Teams, like “Can’t connect” or “Device not found”?
* Have you used Teams on this device before successfully, or is this the first time? (New setups might need permissions granted.)
* Is your internet connection stable during this issue? (Frequent disconnects can cause call issues.)
* If it’s a meeting issue, is it only one meeting or any meeting you try? (Could isolate if a specific meeting link is bad or a general problem.)

**Troubleshooting Steps:**

1. **Basic Restart:** Often, simply quitting Teams and restarting it can fix transient issues (especially after the computer wakes from sleep). If a call is stuck or audio stopped, exit Teams fully (system tray -> right-click -> Quit) and reopen it. You can also try rebooting the computer.
2. **Check Mute/Volume and Devices:** Many “no sound” or “no one can hear me” issues are due to mute or wrong device selection. In Teams, click the **Devices** settings: ensure the correct microphone and speaker are selected. If using a headset, make sure it’s plugged in and chosen. Verify the user hasn’t accidentally muted themselves in the call (and that the other side isn’t muted).
3. **Test in Another App:** Determine if it’s specifically Teams or the device. For instance, can the user record audio or use the microphone in another program? If the mic doesn’t work anywhere, it’s likely a device or driver issue (have they muted the physical mic or is a headset switch off?). If it works elsewhere but not in Teams, focus on Teams settings.
4. **Camera Issues:** If the camera isn’t working, check if the camera is covered or turned off. Then, in Teams settings under **Devices** (or during a meeting under device settings), make sure the correct webcam is selected. If using an external webcam, try re-plugging it. Also, check Windows Privacy settings to ensure apps are allowed to use the camera and microphone.
5. **Update Teams:** Ensure the Teams client is up to date. Click on the profile picture in Teams > **Check for updates**. Teams updates often and may resolve known bugs in audio/video. Also ensure the user’s Windows is updated (sometimes a Windows update can break or fix device drivers relevant to Teams).
6. **Notifications/Chat Issues:** If the issue is not A/V but chat or Teams channels (like messages not showing or notifications missing), try clearing the Teams cache. This can be done by signing out of Teams and deleting Teams cache folders, or a quicker attempt is to use Teams web (teams.microsoft.com) to see if the issue is client-specific. Also, check the user’s status (if set to Do Not Disturb, notifications might be suppressed).
7. **Bandwidth/Network:** Video calls can be choppy or drop if the network is slow. If the user is on Wi-Fi, have them try moving closer to AP or plugging into wired if available. You can also ask them to close other bandwidth-heavy applications. There’s a **Call Health** indicator in Teams calls (three dots > Call Health) that can show network stats if needed. If multiple users in the office have Teams quality issues, consider network bandwidth problems – escalate to network team.
8. **Reinstall or Use Web as Workaround:** If the Teams desktop app seems problematic, have the user try the web version or mobile app to get through their meeting. Meanwhile, you can reinstall the Teams client on their PC (fully uninstall, then reinstall via latest package).
9. **Service Status:** On rare occasions, Teams itself might have a service issue (e.g., an outage in your region affecting meetings or chats). Check Office 365 Service Health for Teams if other users report problems. If there is an outage, inform users it’s on Microsoft’s side and provide temporary workarounds (like using phone for audio dial-in if available).
10. **Escalation:** If device-specific issues persist (like a microphone not working despite all settings looking correct), involve a Tier 2 who can update drivers or explore OS-level settings. For complex scenarios (e.g., only one user cannot join any meetings but others can, possibly account config issue), escalate to the O365 admin or Microsoft support for deeper analysis.

**Common Resolutions & Next Steps:**

* **Device Settings Adjusted:** The most common fixes are simple: unmute the mic, select the correct audio output, or plug in the device properly. Once the user hears sound or is heard in a test call (Teams has a **Test Call** feature under Settings > Devices), the issue is resolved. Document if it was user error (e.g., volume was down or muted) for awareness.
* **Software Reset:** Restarting Teams or the PC often clears up glitches. If after a restart things work (maybe an update applied), consider it resolved. If you cleared cache or reinstalled Teams, monitor if the issue reoccurs.
* **Alternative Used:** In some cases, if a particular device (like a headset) fails, giving the user an alternative (another headset or using the laptop’s built-in mic) gets them operational while the faulty hardware is dealt with. That resolves the immediate problem, and you might follow up by replacing a defective peripheral.
* **Escalation:** For issues that couldn’t be fixed (say the user’s account is bugged or a network QoS issue), ensure the user has a workaround (e.g., use phone for audio) and escalate. The ticket remains open for Tier 2/3 to investigate the deeper cause. They might adjust firewall settings (for network issues) or reset the user’s Teams configuration on the backend. Keep the user updated on progress.

---

## 5. Printer Not Printing

**Description:** Printer issues are a daily headache in many offices. The user might report nothing happens when they print, or the document is stuck in the queue. They could see an offline printer status or error messages on the printer (like paper jam, out of toner). In a Windows/AD environment, printers can be local or networked; either way, printing problems are very common and frustrating for users who need their documents.

**Initial Questions:**

* Is this a **network printer** (shared in the office) or one connected directly to your PC via USB? Which printer (name/model) is it?
* Have you been able to print to this printer before, or is this the first attempt?
* What happens when you try to print – do you see the job in the print queue, any error pop-up, or does it appear to send but nothing comes out?
* Is the printer displaying any error lights or messages (e.g., flashing light, “Paper jam”, “Toner low” on the printer’s panel)?
* Can others print to this same printer, or are multiple people affected? (This helps determine if it’s a user’s PC issue or the printer/printer server.)

**Troubleshooting Steps:**

1. **Basic Checks:** Instruct the user to check the printer’s power and connection. Is the printer turned on? If it’s a local USB printer, is the cable plugged in securely? For network printers, is the network cable connected or Wi-Fi signal strong (if it’s wireless)? Often, printers showing “offline” are simply not connected or powered off.
2. **Printer Status on PC:** Have the user open **Devices and Printers** (or **Printers & Scanners** in Windows Settings). Is the printer in question listed and set as default? Does it show “Ready” or “Offline”? If offline, right-click and try **Use Printer Online**. If it’s paused, resume it.
3. **Queue and Spooler:** If there are jobs stuck in the queue (the user can see their document in the queue window), have them cancel all jobs. Sometimes a single stalled job will block the queue. After clearing, try a test print (e.g., a Word document or a Windows test page).
4. **Run the Troubleshooter:** Windows has a built-in Printer Troubleshooter. Guide the user to Settings > Update & Security > Troubleshoot > Additional Troubleshooters > “Printer” and run it. This might automatically detect issues like spooler problems and fix them.
5. **Restart Print Spooler:** If jobs aren’t printing at all, restarting the Print Spooler service can help. You (or the user if comfortable) can do this: open Services.msc > find **Print Spooler** > right-click **Restart**. Or simply reboot the PC, which will also restart the spooler. After that, try printing again.
6. **Check Printer for Errors:** If the printer itself has an error (blinking light, error code), have someone at the printer check for simple issues: paper jam (clear any jammed paper), out of paper (refill tray), low/empty toner or ink (replace cartridge). Also verify the correct paper tray is selected if the user is printing a special size.
7. **Connectivity (Network Printer):** If it’s a network printer, try to **ping** the printer’s IP from the user’s PC (if known). No reply could indicate the printer is offline or network issues. If the printer isn’t pingable, you might ask the user to power cycle the printer (turn off, wait, turn on) – sometimes network printers drop off until rebooted.
8. **Re-add or Update Driver:** If the printer still won’t respond, remove and re-add the printer on the user’s PC. In **Devices and Printers**, delete the printer, then **Add Printer** to reinstall it (preferably using the proper network path or IP). Windows will apply a fresh driver. If it’s a driver issue, downloading the latest driver from the manufacturer and installing it can help.
9. **Test on Another Account or Device:** If possible, have another user print to that printer, or have the affected user try from a different computer. This can isolate if it’s the printer’s problem or something with the user’s PC/profile.
10. **Escalation:** If none of the above resolves the issue, escalate. For a single user issue, Tier 2 can check deeper (event logs for print errors, spooler config). If it’s a printer hardware fault (e.g., consistently jamming or not responding on the network), you may need to involve the maintenance/vendor or your internal team responsible for printers. Also escalate if it’s a print server queue issue affecting many users – the server or service might need attention.

**Common Resolutions & Next Steps:**

* **Clearing Queue/Spooler:** One of the most common fixes is clearing a stuck print queue or restarting the spooler service. This often immediately frees up the printer to start printing new jobs. Users should then see their documents print and the issue is resolved.
* **User Error or Simple Issue:** Sometimes the printer was set to offline mode, or they were trying to print to “Microsoft PDF” instead of the actual printer. Correcting the printer selection or turning off “Use Printer Offline” fixes those. An unplugged cable or powered-off printer (surprisingly common) when corrected also resolves the problem.
* **Hardware Supply Issues:** If the resolution was adding paper or replacing toner, educate the user on any indicator they can watch for (many printers show a low-toner warning) so they know it was a supply issue. The printer should function after supplies are replenished.
* **Next Steps if Unresolved:** If escalated, the next team might discover a need to reinstall the printer driver on the user’s PC or fix a print server issue. They might also identify a defective printer that needs replacement. Keep the user updated; if printing is mission-critical, consider offering an alternative printer in the meantime. Once fixed, verify by printing a test page with the user and close the ticket.

## 6. Wi-Fi Connectivity Issues

**Description:** Users connecting via Wi-Fi may report they can’t connect to the network or they have a connection but no internet access. In an office with Ubiquiti UniFi wireless, common complaints include dropped connections or “limited connectivity”. Network connectivity disruptions – especially wireless dropouts or slow Wi-Fi – can greatly reduce productivity. This issue can be device-specific or widespread if an access point is down.

**Initial Questions:**

* Are you unable to see the Wi-Fi network at all, or do you see it but cannot connect (or connect with no internet)?
* Is this happening on just your device, or are others around you reporting the same Wi-Fi issue?
* Have you ever connected to this office Wi-Fi successfully before on this device? (If new device or new employee, could be a onboarding/config issue.)
* Did anything change recently – e.g., did you change your AD password (which might affect Wi-Fi if using domain credentials), or was the device recently updated?
* What error or symptom do you get? (“Can’t connect to this network”, or connected with a yellow exclamation “No Internet”, etc.)

**Troubleshooting Steps:**

1. **Check Multiple Devices:** Determine scope. Ask the user if other devices (phone/laptop) can connect to the Wi-Fi. If many devices/users are affected (e.g., everyone on a certain floor), it might be an AP or network outage – skip to escalation in that case because it’s likely not the user’s device. If it’s just their device, continue with client-side troubleshooting.
2. **Have User Toggle Wi-Fi:** A quick fix often is to turn off the Wi-Fi adapter and turn it back on. In Windows, they can click the Wi-Fi icon and toggle Airplane mode on/off, or simply disconnect and reconnect to the SSID. Also, a full **reboot** of the laptop can reset the wireless adapter and often resolves transient issues.
3. **Validate Credentials:** If the Wi-Fi requires a username/password (e.g., enterprise WPA2-Enterprise using AD creds), ensure the user is entering current credentials. A recent password change might mean their stored Wi-Fi password is now wrong, causing authentication failure. Have them “Forget” the network and try connecting fresh with the new password.
4. **IP Address Check:** If the user connects but has no internet, get them to run `ipconfig` in Command Prompt. Check if they got a valid IP (in your organization’s subnet) or an APIPA (169.254.x.x) address. An APIPA means the device did not get a DHCP lease. That could indicate a DHCP server issue or that the device isn’t fully associated with the AP. In that case, step 5 or network side investigation is needed.
5. **Router/AP Health:** Since this is UniFi, if you have access to the UniFi Controller, check if the user’s device is showing as connected or if the AP they use is online. UniFi dashboards can show if an AP is down or if a client is being blocked. If you suspect a specific AP issue (user in a certain area can’t connect but others elsewhere can), a workaround is to have the user move temporarily to a different location to use a different AP, or connect via Ethernet if possible, then notify network team.
6. **Signal Strength & Interference:** Ask where the user is located. If they are in a fringe area (far from AP) or behind many walls, signal could be weak. Have them try moving closer to a known AP location. Also, if there are many SSIDs or interference, sometimes switching frequency (5 GHz vs 2.4 GHz) can help – though this is more advanced: as Tier 1 you might simply note if the device supports both and perhaps forget one band’s network to force the other.
7. **Network Stack Reset:** If it seems specific to the device, you can try resetting the network adapter. In Windows: `ipconfig /release` then `ipconfig /renew` might help if it was a DHCP hiccup. If not, running `ipconfig /flushdns` and `netsh winsock reset` followed by a reboot can fix deeper TCP/IP stack issues. Only do this if basic reconnect fails.
8. **Driver Check:** Ensure the user’s Wi-Fi driver is up to date. If the device recently did an update, maybe the driver rolled back or got corrupted. Tier 1 can guide the user to Device Manager > Network Adapters > (Wi-Fi adapter) and update the driver. If the driver is the issue, sometimes rolling back to a previous version helps (if a new update caused the problem).
9. **Temporary Workaround:** If the user urgently needs connectivity and Wi-Fi is unreliable, and if an Ethernet port is available, have them plug in via cable (if feasible) as a temporary solution. This will at least get them online while Wi-Fi issues are investigated. If not, perhaps a mobile hotspot (if allowed) as a last resort.
10. **Escalation:** For persistent Wi-Fi problems, especially if multiple users are impacted or the cause seems to be on infrastructure (AP offline, DHCP not handing addresses, etc.), escalate to the network team. Provide details: which SSID, which AP (if known), time of issue, any errors observed. The network team can check UniFi controller logs, adjust channels, or restart APs if necessary.

**Common Resolutions & Next Steps:**

* **Reconnection Success:** In many cases, toggling Wi-Fi or rebooting the machine clears a transient glitch and the user reconnects successfully. Once reconnected, have them verify they can reach the internet and internal resources. This quick fix resolves the ticket, but if it recurs frequently, note that and investigate further (could indicate a driver or AP issue).
* **Credential Fix:** If the issue was an incorrect stored password (user changed AD password but Wi-Fi had old one saved), “forgetting” and rejoining the Wi-Fi with correct credentials solves it. Users then can connect normally.
* **DHCP/IP Issue:** If the user got an APIPA, after your troubleshooting they should get a valid IP (you can have them run `ipconfig /all` again to confirm). Once they have a valid IP and can browse, the problem is resolved. If you found that the DHCP service was at fault (maybe it was restarted), monitor in case it happens again.
* **Infrastructure Problems:** If an AP was overloaded or malfunctioning (e.g., clients connected but no IP given), the network team might reboot that AP or adjust its settings. The resolution in that case is on the backend; inform the user that the Wi-Fi access point was reset and to try again. They should connect fine afterward.
* **Escalation Outcome:** In scenarios requiring escalation, the final fix might involve updating firmware on UniFi devices, adding more access points to cover dead zones, or other network changes. Once those are implemented, follow up with the user to ensure their connectivity is stable. If resolved, close the ticket, noting the network team’s action (for example, “AP in conference room was replaced, issue resolved”).

## 7. Wired Network (Ethernet) Connectivity Issues

**Description:** A user connected via Ethernet cable reports no network or internet access. This could manifest as a red X or globe icon on the network status, or they cannot reach any internal resources or websites. Unlike Wi-Fi, a wired connection issues often relate to physical cabling, switch port problems, or IP/DHCP issues. Network connectivity disruptions, whether wired or wireless, need urgent attention as they prevent work.

**Initial Questions:**

* Have you verified the network cable is plugged in firmly to both your computer and the wall port (or docking station)? Any recent desk moves or cable changes?
* Do you see indicator lights on the network port (usually a green/orange LED) when the cable is plugged in?
* Is it just your computer with this issue, or are nearby colleagues (on wired connections) also offline?
* Did the connection ever work on this machine at this location? (If a new setup, could be a port not activated, etc.)
* Are you using any docking station or USB network adapter that could be introducing an issue (if so, have you tried bypassing it)?

**Troubleshooting Steps:**

1. **Physical Inspection:** Ensure the user checks the cable connections. If possible, have them try a different Ethernet cable (the existing one might be faulty). If they have access to another known-working network port (or jack), they can try plugging in there to rule out a bad wall port.
2. **Adapter Status:** In Windows, have the user go to **Network and Sharing Center > Change Adapter Settings** (or simply Network Connections). Is the Ethernet adapter showing as **Disabled** or **Enabled**? If disabled, right-click and Enable it. If it’s enabled, does it show “Network cable unplugged” or “Media disconnected”? If yes, the PC isn’t detecting a link – likely a cable or port issue.
3. **IP Configuration:** If the adapter shows connected but no internet, check IP. Use `ipconfig /all` – does it have an IP address? If it’s 169.254.x.x (APIPA), it means no DHCP lease obtained. Try `ipconfig /release` then `ipconfig /renew` to force a DHCP request. Watch for any error (like “unable to contact DHCP server”).
4. **Bypass/Isolation:** If a docking station or USB NIC is involved, have the user connect the Ethernet directly to the laptop’s built-in port (if available) to eliminate a bad dock. If direct works, the dock could be faulty or need a driver update.
5. **Switch Port Check:** This may require coordination – if you have access to network tools or a colleague in networking, ask if the switch port for that user’s office jack is active and not error-disabled. Sometimes ports shut down due to security (e.g., if a loop was detected). If you suspect this, you might escalate immediately.
6. **Temporary Wi-Fi Use:** While troubleshooting, if the user’s Wi-Fi works (assuming their machine has Wi-Fi), you can ask them to switch to Wi-Fi so they can continue working. This isn’t a fix for wired, but it’s a workaround so they’re not completely offline. Keep focusing on the wired issue in parallel.
7. **NIC Drivers & Power:** Check Device Manager for the network adapter status. If there’s a yellow exclamation, driver might be an issue. Update the driver if needed. Also, sometimes disabling “Energy Efficient Ethernet” or power saving on the NIC (in adapter properties) can resolve intermittent drops, though this is an edge case.
8. **DNS/Connectivity Test:** If the user has an IP address but “no internet”, try to ping the default gateway and a known internet IP (like 8.8.8.8). If ping to gateway fails, it’s local network issue; if gateway works but internet IP fails, maybe firewall or DNS. Then test `ping google.com` to see if DNS resolves. If DNS is the issue, you could set a temporary DNS (like 8.8.8.8) to test connectivity. However, these steps may be advanced for some Tier 1 – use them if comfortable.
9. **Escalation – Network Team:** If after cable swaps, IP renewals, and checks the wired network still is down, escalate. The network team can check the switch port status, VLAN configuration, or any outages. Provide them the user’s port location or switch if known, and the MAC or IP of the machine if it has one. They might discover a faulty switch, a port that needs to be enabled, or a bigger network issue.

**Common Resolutions & Next Steps:**

* **Cable/Port Fixes:** A very common resolution is simply replacing a bad cable or using the correct port. Users sometimes plug into an inactive jack. Once the cable/port is corrected, the PC gets a link (lights on) and an IP address, restoring network access. The user should confirm they can reach both internal sites and the internet.
* **IP Renewal:** If a transient DHCP issue, releasing/renewing the IP often fixes it, granting a valid IP configuration. The user goes from limited connectivity to full network function. Document if DHCP service was possibly hung and monitor if other users had similar issues (which might indicate a server to check).
* **Switch Port Activation:** If the network team finds the port was shut or on the wrong VLAN, and they fix it, the user’s connection will start working. In this case, let the user know it was an infrastructure config issue and it’s now resolved.
* **Alternate Access:** If the immediate solution was to use Wi-Fi, ensure that’s only temporary if the wired issue persists. Wired issues often get escalated, but once resolved (by a port fix or replacing a faulty wall jack, etc.), follow up to have the user revert to wired and test. Wired network usually is preferred for stable connection, so confirm and then close out.
* **Escalation Outcome:** Sometimes the resolution is beyond Tier 1 (e.g., replacing a faulty network switch). In that event, coordinate with facilities or network admins. Once done, verify the user’s connectivity. Provide a summary in the ticket (e.g., “Network switch was replaced by Network Team; user’s connection restored”) and close after confirmation.

## 8. Slow Computer Performance

**Description:** A common complaint is “my computer is running slow.” This can involve slow boot times, apps taking long to open, or system freezes. Such slow or unresponsive systems are a significant source of help desk tickets. Causes can range from insufficient hardware resources, too many background programs, malware, or even a failing disk. Tier 1 should methodically identify if it’s a general slowness or specific to certain software.

**Initial Questions:**

* When did you start noticing the slowness? Did it come on suddenly or gradually over time?
* Is everything slow (the whole system), or only certain applications (e.g., “Excel is slow but other things are fine”)?
* Have you rebooted the computer recently? If not, how long has it been on?
* Are there specific times when it’s worse (for example, right after login, or when opening a particular program)?
* Did you see any error messages or have any recent crashes? (Sometimes slowness could be due to background processes like updates or a nearly full disk giving warnings.)

**Troubleshooting Steps:**

1. **Reboot and Observe:** If the user hasn’t rebooted in a while, have them do a restart. Many times, a simple reboot can clear hung processes and speed things up. After reboot, ask if the system is temporarily better. If yes, slowness might have been due to something accumulating (like memory leaks or many apps open).
2. **Check Resource Usage:** Guide the user to open **Task Manager** (Ctrl+Shift+Esc) and click the **Performance** tab or **Processes** tab. Look at CPU, Memory, and Disk usage. If one of these is consistently high (90-100%), that’s a clue. For instance, if Disk is 100% a lot, the computer might be thrashing (possibly due to an update or low RAM causing paging). If memory is maxed out, maybe too many programs are open or a specific process has a memory leak.
3. **Identify Heavy Processes:** In Task Manager’s **Processes** tab, sort by CPU or Memory to see if a particular process is using a lot. Common culprits include browser tabs (Chrome with many tabs), Office apps with huge spreadsheets, or even antivirus scans. If you find one, have the user save work and close that application to see if things improve.
4. **Disk Space:** Check if the hard drive is nearly full. In File Explorer, have the user look at **This PC** and see the C: drive bar. If it’s red or very close to full, that can greatly slow down a system. Free up space by emptying the Recycle Bin, deleting temp files, or removing unused programs. Aim to keep some 10-20% of the drive free.
5. **Background Programs:** See the system tray – are there many background programs running (e.g., cloud syncs, updaters)? You might disable some non-essential startup programs. In Windows 10/11, Task Manager’s **Startup** tab can be used to disable high-impact startup items (be cautious to only disable known unnecessary ones).
6. **Malware Scan:** Run a full antivirus scan on the machine. Malware can cause high resource usage or network usage, leading to slowness. If any malware is found, remove it (this might solve the performance issue if the malware was using resources).
7. **Windows Updates / Patches:** Sometimes the system is slow because Windows is updating in the background or pending an update. Check Windows Update status. If updates are pending install, schedule a time to do them (as they can hog resources meanwhile). If one is stuck, that could cause issues – possibly trigger the update manually.
8. **Hardware Consideration:** Determine the specs: how much RAM and what CPU does the PC have? If the user is doing heavy tasks (like running VMs, huge spreadsheets) and they have only 4GB RAM, that might simply be inadequate. In the short term, closing other apps can help. In the long term, you might recommend a RAM upgrade or newer PC – escalate this to management if needed.
9. **Temporary File Buildup:** Use Disk Cleanup or CCleaner (if allowed) to clear temp files. A lot of temp/junk files can slow certain operations. Also clearing browser caches if the slowness is web-related might help.
10. **Escalate for Advanced Diagnostics:** If the slowness is persistent and not obviously caused by software, there might be hardware issues (like a failing HDD that retries operations a lot). Tier 2 can run diagnostics (check SMART status of disk, run a memory test, etc.). If it’s an SSD vs HDD issue (HDDs are generally slower), a possible improvement is moving to SSD – but that’s a hardware change requiring approval. Escalate with documentation if you suspect this.

**Common Resolutions & Next Steps:**

* **Simple Reboot/Close Apps:** Often, the user might not have restarted for days and had numerous apps open. A reboot flushes memory and after that the system runs faster. Educate the user on periodic restarts and not running too many heavy applications simultaneously. If that resolved it, close the ticket but note if it’s a recurring pattern.
* **Resource Hog Identified:** If Task Manager showed, for example, an update service eating CPU, once that completes (or if you ended the task or update) the system returns to normal. Or if a particular app (say Chrome) was using 2GB RAM due to many tabs, once closed, performance improves. The resolution is to manage that resource use (maybe advise using Edge or fewer tabs, etc.).
* **Freed Disk Space:** If low disk space was the cause and you cleared it (deleted 50GB of old data), the system will speed up. The user should notice a big difference. Remind them to keep some space free and perhaps use OneDrive or archive old files.
* **Malware Removal:** If a virus was found and removed, the performance issue may be solved (and you’ve addressed a security problem too). After removal, monitor the system for any remaining issues. It’s resolved if the system runs normally, but remain alert if more malware symptoms appear – that might need a deeper clean or reimage.
* **Next Steps – Hardware Upgrades:** If slowness is due to genuinely insufficient hardware, you might not “resolve” it immediately. In that case, escalate a recommendation (e.g., upgrade from 4GB to 8GB RAM, or swap HDD to SSD). In the meantime, you might adjust virtual memory or remove bloatware to help. Communicate to the user that you’ve done what you can and that you’re proposing an upgrade for a permanent fix. Keep the ticket open if needed until that is actioned, or close it with note of recommendation depending on policy.

## 9. Blue Screen of Death (BSOD) / System Crash

**Description:** A **Blue Screen of Death** is when Windows crashes and shows a blue error screen, often with a stop code. This indicates a critical failure (like a driver fault or hardware issue). Users typically say “my computer just crashed” or “I got a blue screen.” Most users don’t catch the specific code, as it reboots quickly. OS errors and crashes are very frustrating, and while an occasional BSOD might be a fluke, repeated ones need prompt attention to avoid data loss.
I did not receive the two 20 lb bags of bird seed
wer
**Initial Questions:**

* What exactly happened when it crashed? Did the screen turn blue with an error message, or did the PC just suddenly restart?
* Do you recall any specific error code or message on the blue screen (e.g., “IRQL\_NOT\_LESS\_OR\_EQUAL”, “Memory Management”, or a file name)? Even a partial clue can help.
* Were you doing something specific at the time of the crash (plugging in a device, running a certain program)? Does it happen consistently with a particular action?
* Have you added any new hardware recently (new USB device, RAM, etc.) or updated any drivers?
* How often has this occurred? (One-time event vs happens multiple times a day or week.)

**Troubleshooting Steps:**

1. **Ask for Error Details:** If the user did note the stop code or any message on the BSOD, write that down. If not, no worries – you can still proceed. (If accessible, check **Event Viewer** > System logs for a crash entry or use a tool like BlueScreenView to get the code, though that may be Tier 2’s role.)
2. **Check Recent Changes:** If the user recently updated something (Windows update, driver, or installed software), that could be the culprit. For instance, a new graphics driver might cause BSOD. Consider rolling back that driver: in Device Manager, find the device (like Display adapter) and choose “Roll Back Driver” if available, or use System Restore to a point before the update if the timing aligns.
3. **Hardware Peripheral Disconnect:** If a specific peripheral triggers it (e.g., “Every time I plug in my USB headset, I get a blue screen”), avoid using that device and update its drivers. Try a different port or device. If a docking station is involved (common with BSOD on resume), ensure its drivers/firmware are updated.
4. **Safe Mode Stability:** In cases of frequent BSODs, have the user boot into **Safe Mode** (with Networking if needed). Safe Mode loads minimal drivers. If the PC is stable in safe mode, the issue is likely a driver or software service that doesn’t load in safe mode. That can guide you – e.g., if normal mode BSODs but safe mode doesn’t, you might disable recently added startup programs or drivers.
5. **Run Diagnostics:** If hardware is suspect, run basic diagnostics. For memory, Windows has a Memory Diagnostic (run `mdsched.exe`); for disks, run `chkdsk` or check SMART status for errors. Tier 1 can initiate these, though if errors are found, it typically goes to Tier 2 to replace hardware.
6. **Scan for Malware:** Although less common, some malware or a virus can cause system instability. Running a malware scan in safe mode or after reboot can rule this out as a cause.
7. **Apply Updates/Patches:** Ensure Windows is fully updated. Sometimes Microsoft releases fixes for known blue screen causes. Conversely, if a BSOD started *after* a specific Windows update, note that – you might temporarily uninstall that update to test (this might be Tier 2’s call if it’s a complex patch).
8. **System File Check:** Run `sfc /scannow` in an elevated Command Prompt to check for corrupted system files. If it finds and fixes issues, that could resolve crashes due to OS file corruption. Also, `DISM /Online /Cleanup-image /Restorehealth` can fix component store issues (advanced step).
9. **Document and Escalate:** If the BSODs persist despite basic troubleshooting, escalate to Tier 2. Provide them with any info gathered: error codes, what triggers it, steps taken. They may need to analyze memory dump files to pinpoint the driver or module causing the crash. In some cases, hardware replacement (faulty RAM, failing disk) might be the resolution.

**Common Resolutions & Next Steps:**

* **Driver Updates or Rollbacks:** A very common cause of BSODs is driver problems. If you identify, for example, that the network driver is causing BSOD, updating it to the latest version often fixes it. Sometimes *rolling back* to a previous stable driver if the newest one is buggy is the solution. Once changed, monitor to see if BSOD stops. If it does, resolved – note which driver was the issue.
* **Removed Faulty Hardware:** If a particular device (e.g., a bad docking station or USB device) is causing crashes, not using it (or replacing it) resolves the BSOD. For instance, if a RAM stick is bad, removing/replacing it stops memory errors. This resolution might involve hardware service which is usually done by Tier 2, but Tier 1 plays a role in identifying the suspect.
* **System Repair:** If system files were corrupted (and SFC/DISM fixed them, or you did a System Restore), the system may become stable. Continue to have the user use the PC and check in – if no more blue screens over a couple of days, it’s likely resolved. Log that a system repair was done.
* **Escalation:** For persistent blue screens that Tier 1 cannot resolve, the next steps could be a full OS reinstall if no root cause is found, or hardware diagnostics by Tier 2. If Tier 2 resolves it by, say, replacing the motherboard or re-imaging Windows, then that’s the final resolution. Ensure the user’s data is backed up if any drastic steps are taken. After the fix, verify with the user that the crashes have ceased, then close the ticket.

## 10. Application Crashing or Not Responding

**Description:** Users often encounter a specific program that keeps crashing or freezing. For example, “Excel is not responding when I try to save” or “Our ERP application closes unexpectedly.” Such software glitches disrupt work. This issue may be isolated to one app while others work fine. Causes range from software bugs, corrupt user profiles or settings for the app, to insufficient resources for that application.

**Initial Questions:**

* Which application is having the issue? (e.g., Word, Excel, Outlook, a web browser, or a specialized software)
* What exactly happens – does it freeze (“Not Responding”) or does it crash/close on its own? Any error message or error code shown?
* Is there a specific action that triggers it (e.g., “it crashes whenever I try to print” or “whenever I open a certain file”)?
* Has this application ever worked correctly on this machine for this user? If yes, when did it start acting up (after any update or change)?
* Are other users experiencing the same problem with this application, or is it just this one user? (If others, it could be a broader software issue or update needed.)

**Troubleshooting Steps:**

1. **Reproduce and Isolate:** If possible, have the user demonstrate what causes the crash. Sometimes seeing the sequence helps. If it’s a large file causing Excel to freeze, that hints the issue might be file-specific. Check if the issue happens with any file or just one. If just one, that file might be corrupted.
2. **Check for Known Issues:** For common apps like Office, certain crashes are known (e.g., Word sometimes has add-in issues). Ask if any add-ins or plugins are in use. Try starting the app in safe mode (for Office, hold Ctrl and click the app to get Safe Mode). If it doesn’t crash in safe mode, an add-in is likely the culprit – disable add-ins one by one.
3. **Update the Application:** Ensure the app is up to date with the latest patches. For Office 365 apps, go to File -> Account -> Update Options and update. For third-party software, check if an update or patch is available on their website that addresses crashes. Many times, developers release fixes for known bugs causing crashes.
4. **Repair the Application:** In Windows, for Microsoft Office you can do an **Online Repair** (via Control Panel > Programs > select Office > Change > Repair). For other apps, see if there’s a repair option or consider reinstalling the application. Repairing will fix any corrupted program files.
5. **Clear Cache/Temp Files:** Some apps keep caches or temp files that, when corrupted, cause issues. For example, if a browser is crashing, clearing the cache or a new user profile in the browser can help. If it’s something like Teams or an Adobe app, they might have specific cache directories – you can often find these in app documentation to clear them.
6. **Verify Resources:** If the app is crashing due to running out of memory (e.g., very large Excel file on a low-RAM PC), observe Task Manager when it’s in use. If memory shoots up to near 100% then crash, it’s resource exhaustion. The immediate fix might be to close other programs to free memory or use a smaller dataset. Long-term fix could be more RAM or a 64-bit version of Office (which can handle larger files).
7. **Event Viewer Logs:** Check **Event Viewer** > Windows Logs > Application around the time of the crash. Often there will be an error entry with the app name or a .DLL that faulted. This can give clues (for example, if it’s an Outlook crash and the faulting module is ntdll.dll or a specific add-in DLL). This step might be a bit advanced for a new tech, but it’s useful if previous steps fail.
8. **Test with Another User or Machine:** If possible, see if the same issue occurs under a different user login on that PC, or on a different PC with the same file. For example, if one user’s Outlook crashes on opening calendar, but another user on that machine doesn’t, it could be something in the user’s mailbox or profile. Alternatively, if on another PC the same file crashes Excel, the file is likely the problem. This helps pinpoint user-specific vs global issue.
9. **Reinstallation:** If the app continues to misbehave, uninstall and reinstall it. Make sure to back up any user-specific settings if needed (like export their config if it’s an app that has that). A fresh install can often clear unexplained crashes.
10. **Escalation:** If this is a specialized line-of-business application and crashes persist, escalate to the team or vendor that supports that software. Provide them any error messages or logs. They might have known solutions or might need to apply a patch. If it’s an in-house app, the developers may need to get involved. Meanwhile, consider if there’s a workaround (like using an alternative program) so the user can continue working.

**Common Resolutions & Next Steps:**

* **Restart or Simple Fix:** Sometimes the classic “turn it off and on” applies to apps too. If an app was running for a long time, a restart of the app or the computer might clear the issue. If that fixed it, great, but keep an eye if it recurs – might need deeper look if frequent.
* **Update/Repair Success:** Many application issues are resolved by updating to the latest version or performing a repair install. After an update/repair, have the user test the previously crashing action. If it no longer crashes (e.g., Excel now saves that file without issues), then it’s resolved. Document that an update or repair was done.
* **Removed Add-ons:** If disabling or removing a problematic add-in (say a third-party Outlook plugin or a browser extension) stops the crashes, then that’s the fix. Inform the user which add-on was causing it and if possible find an updated version or alternative if they need that functionality.
* **Workaround & Escalation:** If a full fix isn’t immediate, sometimes giving the user a workaround stops the bleeding. For instance, “Excel keeps crashing with this one file – as a workaround, split the file into two smaller ones or use a different PC that has more memory.” These are temporary. Meanwhile, escalate to get a proper resolution (e.g., more RAM, or file cleaned by the application team).
* **No reoccurrence:** If the app crash was a one-time event and cannot be reproduced, it might have been a fluke. In such cases, after basic checks, advise the user to monitor and call back if it happens again. Many times, a random crash isn’t reported again. You can tentatively close the ticket, noting no issues found now, and that they will reopen if it recurs. If it does recur, then escalate with the pattern of frequency.

## 11. Software Installation or Update Issues

**Description:** Tier 1 frequently handles requests where a user needs a new software installed, or an installation is failing with errors. This category includes missing software, failed installs, or updates that won’t apply. Such “software installation and upgrade headaches” are common. In a managed environment, users often don’t have admin rights, so they can’t install software themselves. This becomes a ticket for IT to either push the software or guide through the process.

**Initial Questions:**

* What software are you trying to install or update? (Name and version)
* How are you installing it (self-service portal, direct download, via company software center)? And what error or behavior do you see when it fails?
* Do you have administrator privileges on your machine? (Most likely not – which means IT will need to assist with the install.)
* Is this a new installation or an update to an existing application? If update, what version are they on vs trying to get to?
* Is this software licensed or approved by IT? (If not sure, this might trigger an approval workflow.)
* (If update issue) Are other users having similar issues updating the same software? Sometimes company-wide issues occur if a patch is problematic.

**Troubleshooting Steps:**

1. **Permission Check:** Determine if the user has rights to install. If not, typically the help desk or an admin will have to perform the installation. Use remote support or on-site support to execute the installer with admin credentials. If company uses a software deployment tool (like SCCM or Intune or a software portal), ensure the user knows to get it through that channel rather than downloading random installers.
2. **Storage and Requirements:** Ensure the PC meets the software requirements. Check if there is enough disk space for the installation. Also verify OS version compatibility (e.g., some software might require Windows 10 64-bit, etc.). If the installer is failing, sometimes it’s due to .NET framework or other prerequisites missing – watch error messages for clues.
3. **Installer Integrity:** If the user manually downloaded an installer, it could be corrupted. Re-download from a trusted source or from the vendor’s official site. If possible, use the IT-provided package (to ensure it’s the right edition/license).
4. **Run as Admin:** If the user did try themselves and it gave errors, try running the installer as an administrator (right-click > Run as Administrator). This can bypass some permission issues if the user account is a standard user but you have a local admin account to input.
5. **Check for Existing Versions:** If it’s an update, sometimes older versions or partial installs cause conflicts. Uninstall the old version first (if appropriate) via Programs & Features. Then try a fresh install of the new version. If an uninstall also fails, you may need a cleanup tool (many vendors provide cleanup utilities for failed installs).
6. **Observe Error Codes:** If an installation fails with an error code, note it. A quick search in the knowledge base or vendor site for that code can yield specific solutions (e.g., MSI error 1603 is a generic install failure – often needs more logging; error about .NET might mean install .NET 4.8 first, etc.).
7. **Use Compatibility Mode:** If installing an older software on a newer OS, try compatibility mode (right-click installer > Properties > Compatibility). But this is rare in corporate environments as software should be modern.
8. **Updates Failing (Windows or Office):** If Windows Updates are failing, run the Windows Update Troubleshooter. For Office 365 updates failing, you might do an Office Quick Repair or ensure the user’s internet is working (Office updates need online access). Sometimes manually downloading the update patch and installing can bypass whatever issue the automated updater had.
9. **License or Account Check:** If the issue is that the software installed but won’t activate (like Office showing unlicensed), ensure the user has a valid license assigned. For Office 365, have them sign in with their work account to activate Office. If a user tries to install software they aren’t entitled to, that’s a different conversation – might need purchasing or management approval.
10. **Escalation:** If an installation consistently fails and it’s not a simple permission or installer issue, escalate to Tier 2. They might need to do deeper digging (like examining MSI logs, adjusting group policy, or using admin tools to force install). Also escalate if it’s a package that needs repackaging or if it’s clearly an enterprise software that requires server-side setup (like installing a database client that needs configuration).

**Common Resolutions & Next Steps:**

* **Successful Install by IT:** In many cases, the “issue” is simply that the user needed IT to install the software due to permissions. You remote in (or use admin credentials) and complete the install. The application launches fine for the user – ticket resolved. Advise the user to use official channels next time (like raising a request) if they need new software so IT can assist.
* **Installation Error Fixed:** If a specific error was resolved (for example, installing a required framework or updating Windows, then the software installed successfully), note that solution. The user can now use the software. If the resolution involved a workaround (like “had to install in compatibility mode” or “had to create a new local admin account to run installer”), document it and monitor if similar issues occur for others, which might indicate a broader image or policy problem.
* **License Assigned:** If the problem was an Office app saying unlicensed, assigning the proper Office 365 license to the user or having them sign in with the correct account will activate it. Once Office (or other licensed software) is activated and working, that ticket is done. Remind the user that if they ever see unlicensed, it could be account issues – and to always use their corporate account for such software.
* **Scheduled or Deferred:** Sometimes, if a user requests a non-standard software, Tier 1 might not install it immediately (especially if not approved). In that case, the resolution is coordinating approval and scheduling installation. E.g., “Software X was approved by manager, and installed via IT on date Y.” Make sure the user is aware of the timeline. After installation, verify it runs. Then close the ticket noting the approvals.
* **Escalation Outcomes:** If escalated (for example, a complex database client needed configuration), Tier 2/3 might complete the install. The resolution to the user is the same – they now have a working program – but note in the ticket if any special config was done. Ensure any installation media or documentation gets updated if this was a new issue.

## 12. VPN Connection Issues

**Description:** When working remotely, users often rely on a **VPN** (Virtual Private Network) to access corporate resources. Common VPN issues include inability to connect to the VPN server, frequent disconnects, or not reaching internal sites even when connected. As remote work increases, “VPN not working” tickets have become more frequent. These issues can be due to incorrect login, network problems, or configuration issues on either the client or server side.

**Initial Questions:**

* What VPN client or method are you using to connect (e.g., a specific application like Cisco AnyConnect, OpenVPN, L2TP built-in client, etc.)?
* What error message do you get when it fails? (For instance, “server not responding,” “authentication failed,” or it hangs at a certain step.)
* Have you been able to use the VPN successfully before on this device/network, or is this the first time set up?
* Are you on a stable internet connection right now? Can you browse normal websites without VPN? (We need to ensure their general internet is working first.)
* Did you recently change your network password or make any changes to your laptop? (Cached credentials or updated client software could matter.)

**Troubleshooting Steps:**

1. **Verify Credentials:** The most common cause for VPN connection failure is incorrect credentials. Double-check the username (often corporate email or domain\username) and password. If the user recently changed their AD password, ensure they’re using the new one. Some VPNs require a separate VPN-specific password or PIN – confirm the process (and if they forgot that, a reset might be needed).
2. **MFA/Token Issues:** If your VPN requires MFA (multi-factor, like a code or push approval), ensure the user is completing that step. Sometimes users miss the prompt on their phone or have an old token. If they are not receiving the code, that could be the issue – treat it like an MFA problem (see Issue #15).
3. **Check Internet Connectivity:** Make sure the user’s underlying internet is fine. Have them try loading a webpage. If their internet is down or very slow, the VPN won’t work. If on home wifi, maybe have them reboot their router or try a wired connection. Once general connectivity is confirmed, retry VPN.
4. **Restart VPN Service:** Often, the user can just **disconnect/reconnect** or close the VPN app and reopen it. If it’s hung, kill the app via Task Manager and launch it fresh. If using the Windows built-in VPN, sometimes a reboot of the PC clears stuck states in the VPN adapter.
5. **Common Error Responses:** If the error is “VPN server not found/reachable,” check that they have the correct server address and that their internet (or sometimes firewall) isn’t blocking it. If they’re on a public network, ensure no firewall is blocking VPN protocols (some public wifis block VPNs). As a workaround, they might try using a mobile hotspot to see if the network was the issue.

   * If the error is “authentication failed,” it’s likely credentials or account issue (could their VPN account be locked or not entitled? Some organizations require specific permission for VPN – verify their account is in the VPN user group).
   * If it connects but internal resources still can’t be accessed, it could be split-tunnel config or DNS issues – try accessing by IP if possible. Also verify if the VPN client shows bytes transferring or not.
6. **VPN Client Health:** Ensure the VPN client software is up to date. If it’s an older client, maybe it’s not compatible with the OS or recent updates. Download the latest version from the company’s portal if available. If the client seems corrupted, uninstall and reinstall it.
7. **Configuration Review:** For a manual L2TP or PPTP setup, check the settings (pre-shared key, server name, encryption settings). A common issue is when user’s home network uses the same IP range as the office (e.g., both use 192.168.1.x) causing routing conflicts. If that’s suspected, connecting from a different network or changing the home router’s subnet can solve it – but that’s advanced and usually a network team determination.
8. **Logs and Error Codes:** Many VPN clients have logs. For example, Cisco AnyConnect might give an error code (like “Reason 442: failed to enable virtual adapter”). If you see such known ones, you can search the internal KB or internet for that code. (442 for example has a known fix involving disabling Internet Connection Sharing service). Use those specific solutions if applicable.
9. **UniFi Specific:** If the company uses Ubiquiti UniFi’s VPN (e.g., L2TP to a UniFi Security Gateway), ensure the user’s account is configured for remote access on the controller. If others are connected but this user can’t, perhaps their account isn’t in the VPN group or their shared secret is wrong. You might need to coordinate with whoever manages the UniFi to check logs (UniFi logs might show “bad username/password” or “account not found”).
10. **Escalation:** If basic credential and connectivity checks don’t solve it, escalate to network/security team. There could be an issue on the VPN server side (license limit reached, service hung) or the user’s account might need to be enabled for VPN. Provide the error details and steps tried. If it’s an urgent case (user completely blocked from work), escalate as high priority. Meanwhile, if feasible, suggest alternate access: maybe certain tasks can be done via remote desktop through a jump box if the VPN is down, etc., depending on company setup.

**Common Resolutions & Next Steps:**

* **Credential Correction:** In many cases, the resolution is as simple as using the correct password or re-entering credentials carefully. Once the user enters the right info (or unlocks their account if too many bad attempts locked it), they connect successfully. Educate the user on keeping their VPN credentials in sync (often it’s the same as AD domain password; if so, remind them VPN password changes when AD password changes).
* **MFA/Token Resync:** If the user’s authenticator app was out of sync or they got a new phone, resetting MFA for VPN access solves it. After they’re set up properly, they can connect. This overlaps with MFA troubleshooting – ensure they test it while on the call (e.g., see that their phone gets a prompt and they approve it).
* **Client/App Fix:** If reinstalling or updating the VPN client was done, and afterwards the user connects fine, note that the client was repaired. Possibly other users might need the same update – proactively check if a known bug existed. Now that the user’s VPN is working, they can access internal systems and the ticket is resolved.
* **Network/Server Fix:** If escalated, perhaps the network team found the VPN server had an issue (often when *all* users are affected). They might restart the VPN service or increase capacity. Once that’s done, confirm the user can connect. In this case, inform the user it was a system issue and it’s now resolved. If the user’s AD account wasn’t in the VPN group and now it is, tell them “Your account has been enabled for VPN, please try now” – they’ll connect and you close the ticket, noting the access was granted.
* **Workarounds:** If no immediate fix, sometimes instructing the user to try a different network (some home ISPs or countries might block certain VPN protocols) can help. For instance, “If you’re on hotel Wi-Fi and VPN fails, try using your phone’s hotspot.” That might get them connected in the moment. Long-term, the solution might be implementing an alternative VPN method (like SSL VPN on port 443 which is usually open). Workarounds are not final resolutions, but if one was used, mention it and that the permanent fix is in progress.
* **Follow-up:** For recurring VPN issues (e.g., connects but drops often), monitor patterns. It could be the user’s ISP instability. You might advise them to contact their ISP or try a different router. That goes beyond typical IT support, but sometimes is necessary to mention. If multiple users in a region have VPN problems, escalate to see if a new VPN endpoint closer to them or different configuration is needed. After resolution, ensure the user is satisfied that their remote access is reliable.

## 13. Cannot Access Network Drive or Shared Folder

**Description:** In corporate environments, users often access files on shared network drives (e.g., an SMB file server or a mapped drive letter like S: or Q:). A common issue is “I can’t access my network drive” or “I get access denied to a folder I used to have.” This falls under access issues to shared resources. It could be due to missing permissions, the drive not being mapped properly, or the server being down. It’s a frequent request for help desk to restore connectivity to shared files.

**Initial Questions:**

* What is the path or drive letter of the network resource you cannot access? (e.g., “Drive X: which maps to \Server\DeptShare”)
* What happens when you try to access it – do you get an error message like “Access Denied” or “Network path not found”? Or is the drive simply not showing up at all?
* Have you been able to access this share before, or is this your first time needing it? (If first time, it could be a permission setup issue; if previously had access, something may have changed.)
* Are you in the office network or connected via VPN when attempting this? (If remote without VPN, they won’t reach internal shares.)
* Can other colleagues access the same location? If you don’t know, and it’s urgent, maybe ask a co-worker to test if possible – that helps see if it’s a user-specific problem or the share/server is down.

**Troubleshooting Steps:**

1. **Confirm Path and Connectivity:** Have the user verify the network path. Sometimes users click a drive letter that’s red X (disconnected). In Windows Explorer, if the drive is disconnected, have them double-click it to reconnect. If it says path not found, try manually navigating: Windows Key + R, then type `\\ServerName\ShareName` as provided. See if that opens. If it prompts for credentials, that could mean their saved credentials failed – have them enter their AD username/password.
2. **Permission Check (Logic):** If the error is “Access Denied,” it usually means their user account doesn’t have permission on that folder. Check if the user recently changed departments or roles – perhaps their access was removed or never granted. In AD or the server’s security groups, see if the user is a member of the group that grants access to that share. If not, that’s likely the issue. If yes, perhaps their permissions are correct and the error is misleading, so continue troubleshooting.
3. **Re-map the Drive:** If the drive mapping is missing or incorrect, map it again. Right-click This PC > Map Network Drive, choose a letter and enter the UNC path (\Server\Share). If it maps successfully and they can browse, problem solved – sometimes logon scripts fail or user was on a new PC that didn’t have the drive mapped. If it fails to map, note the error (again, could be not found or access denied).
4. **Credentials Reset:** In some cases, Windows might have cached wrong credentials. You can clear saved credentials via **Credential Manager** (in Control Panel). Remove any stored credentials for that server. Then when trying to access, it will prompt fresh – enter the correct domain\username and password. This can fix weird access issues when Windows was using old credentials behind the scenes.
5. **Network Connection/VPN:** Ensure the user is actually connected to the network where the share resides. If they are remote, they **must** be on VPN (unless the share is in cloud). Many times, the “issue” is simply the user forgetting to VPN in. If so, have them connect VPN and try again. If they are on VPN and still not working, possibly the VPN is not allowing that network – that would be a network config to check.
6. **Check Server Status:** If multiple users cannot access the same server/share, the file server might be down or unreachable. Ping the server name from your station or have the user do it. If ping fails (and it’s supposed to respond), there could be a server issue – escalate to server team. If ping works but share doesn’t, maybe the server service is hung. Try connecting via IP (e.g., \10.x.x.x\Share). If IP works but name doesn’t, could be a DNS issue or WINS. Flushing DNS (`ipconfig /flushdns`) on the client might help in that scenario.
7. **Different User Test:** If possible, have the user log off and have someone else log onto their computer to test accessing that share (or vice versa: user logs onto a different computer). If another user with proper permissions can access it from the same PC, then the network path and PC are fine – it’s the user’s permissions. If our user logs into another PC and still can’t, definitely permission or account issue. This test helps isolate PC vs account issue.
8. **Restore/Migrate Data Scenario:** If the share was recently moved to a new server or a new path (maybe IT upgraded the file server), the old mapping might be broken. Check communications or IT notices – perhaps the user missed an update that the path changed. Redirect them to the new path if so. (You might find that \Server\Share changed to \NewServer\Share – in such case mapping to NewServer resolves it.)
9. **Escalation (Permissions):** If it truly is a permission issue (user not in the right AD group or needs access), escalate to whoever approves and grants such access. Often a manager’s approval is needed. As Tier 1, you might gather the request details and forward to sysadmin or follow your process to add the user to the appropriate group (if you have rights and approval). Document that access was requested. This may not be instantly resolved if approvals take time – keep user updated. If it’s urgent, sometimes a quick temporary access can be given while formal approval is underway.
10. **Follow-up:** After resolving, have the user verify they can open the folder and read/write files as needed. Sometimes they might say “I can see the folder now but one subfolder says access denied.” That could mean deeper folder permission issues – possibly a separate request to grant them that subfolder. Handle similarly by checking if another group controls that subfolder. Each distinct permission might require action. Escalate if needed to get proper access set up by the file server admins.

**Common Resolutions & Next Steps:**

* **Mapped Drive Restored:** Often, simply re-mapping the drive or reconnecting it solves the immediate problem (like after password changes, Windows can drop mappings). The user can access their files again. If this recurs (like every reboot they have to re-map), then possibly the logon script isn’t running – that could be escalated to see why. But one-time, just consider it resolved and instruct the user to let you know if it happens again.
* **Permission Granted:** If the user was missing proper permissions and you or another admin added them to the correct AD group or gave explicit permission, the user can now access the folder. For example, adding them to the “Finance Shared Drive Access” group then they reconnect and it works. This resolves the ticket. Make sure to log that you granted access per policy (and keep any approval records).
* **User Education/Procedure:** If the issue was user trying to access something they never had, and policy says “Submit an access request”, guide them through that process for next time. In this case, the resolution is also telling them, “you didn’t have access, now requested/granted.”
* **Network/VPN Fix:** If the resolution was the user connecting to VPN or coming back on-site, it might be more of a misunderstanding than a tech fix. Still, it resolves their inability to access the share from outside. Remind them for future reference that shared drives require VPN when remote.
* **Server Issue Resolved:** If an outage was involved (file server was down), once IT brought it back online, all users regain access. Communicate to the user “The server was restarted and is now accessible, please try again.” They confirm it’s working – ticket closed. In such cases, often multiple tickets come in; linking them to the incident is useful.
* **Escalation Outcome:** In a complex scenario (maybe a DFS path issue or corruption of the share permissions), Tier 2/3 might handle it. Once they report it fixed (maybe they repaired DFS referrals or reset NTFS permissions), verify the user’s access. Then explain to the user that the technical issue was resolved and they should be good to go. Document the fix (like “Rebuilt permissions on folder X” or “AD group membership updated”).

## 14. Email on Mobile Device Not Working

**Description:** Many users access their Office 365 email on mobile devices (through Outlook app or native mail apps). A common issue is email not syncing or new messages not appearing on their phone. They might say “my phone isn’t getting emails anymore” or “I can’t set up work email on my new phone.” These issues can arise from password changes, device management policies, or mail app configuration problems. In a mobile world, such connectivity issues are common.

**Initial Questions:**

* What type of phone and app are you using? (e.g., iPhone with Mail app, or Android Outlook app, etc.)
* Did it ever work on this device? If yes, did it stop working recently (possibly after something like a password change or phone OS update)?
* Are you getting any specific error messages on the phone (like “Cannot connect to server” or requests to enter password)?
* Can you receive other data on the phone (internet access is working on the device)? This checks it’s not a connectivity issue like cellular data off or such.
* If it’s a new device or recently reset, have you enrolled it in the company’s mobile device management (MDM) if required? Sometimes company policies require device registration.

**Troubleshooting Steps:**

1. **Password and Account Verification:** The most frequent cause for mobile email sync issues is an invalid password (e.g., user changed their AD/O365 password but didn’t update the phone’s mail settings). If the user’s account password changed recently, remove and re-add it in the mail app with the new password. For Outlook app specifically, it should prompt to re-enter credentials; have them do that.
2. **Remove/Re-add Account:** A quick fix is often to remove the email account from the device and add it back. On iOS Mail: Settings > Accounts > Delete the Exchange account, then re-add a new Exchange/Office 365 account with their credentials. On Android or Outlook mobile app, similar – delete the account profile and re-add. This resets the connection and often resolves lingering config issues.
3. **Check Internet and Push:** Ensure the phone has internet access when not on Wi-Fi too (if they rely on cellular). Sometimes, if they only check on Wi-Fi and that Wi-Fi has issues, they think email is down. Also check that in Settings, the Mail has permission to use Cellular Data. If they turned off cellular data for certain apps, that could be it.
4. **Mail App Choice:** If one app isn’t working, try another. For example, if iOS Mail isn’t syncing, have them install Outlook app (or vice versa). If Outlook app works, then the issue might have been the native app needing a specific setting (like enabling “Modern Authentication” or an app-specific password if basic auth is disabled). Using Outlook app often bypasses some of those issues since it’s designed for O365.
5. **MDM or Conditional Access:** If your company uses Intune or another MDM, check if the device is properly enrolled and compliant. Sometimes, email access is blocked if the device isn’t managed or doesn’t meet security requirements (like PIN code or encryption not enabled on phone). The user might have ignored an enrollment step. Guide them to install the Company Portal app (for Intune) and complete device registration. Once compliant, email should start syncing.
6. **Outlook App Reset:** If they use Outlook mobile and it’s acting up, one trick is to reset the app. In Outlook app settings, you can remove the account and add it again (which we covered). Also, ensure notifications are on if the complaint is not seeing new mail notifications. Outlook app also has a “Reset Account” option under troubleshooting in settings which can be tried.
7. **Mailbox issues:** Check if their mailbox is accessible via web on the phone’s browser or a computer. If they can’t log in to OWA with that account, then it’s not a phone problem but an account problem (like account disabled or license issue). If OWA works, the account is fine; focus back on device.
8. **App-specific Password (for older devices):** If the user is on a device that doesn’t support modern authentication and the org has MFA enabled, the user might need to use an app-specific password. This is often the case with older native mail apps. Confirm if your environment requires it. If yes, have them generate an app password in O365 security settings and use that for the mail setup.
9. **Update OS/App:** Ensure the phone’s OS and the mail app are up to date. An outdated mail app might have known sync bugs. Updating iOS/Android or updating the Outlook app to the latest version can solve weird issues.
10. **Escalation:** If normal setup isn’t working (especially if this is a new phone for an exec or something urgent), escalate to the messaging team or Intune team. They can check device logs or if the account is being blocked (maybe by an ActiveSync quarantine). In Office 365 admin, you can see mobile device details for a user – see if the device is listed or blocked. The higher tier can also look at Exchange ActiveSync logs or conditional access logs. If needed, they might do a full wipe/re-enroll of the device and set it up fresh.

**Common Resolutions & Next Steps:**

* **Reconfiguration Success:** In most cases, removing and re-adding the mail account on the phone resolves the sync issue. The mail starts flowing after re-setup. Make sure the user sees the latest emails and can send an email successfully as a test. If yes, ticket resolved. Advise them to always update their mail app password when they change their main password (if not using Outlook app which usually prompts automatically).
* **MDM Enrollment:** If the user’s device wasn’t compliant, once we enrolled it properly (e.g., installed Company Portal, set a PIN, etc.), the email started syncing. The resolution is essentially “device was not enrolled/compliant, after compliance, access is now granted.” Educate the user that this is a security requirement and now that it’s set, it should be smooth.
* **Switching Apps:** Sometimes the solution is telling the user to use Outlook mobile instead of the native app (or vice versa if one is problematic). If that solved it, then the underlying problem might be left unsolved, but at least the user has their email. Often IT standardizes on Outlook app anyway for better support. If the user is okay with the new app, consider it resolved and note it as a workaround/solution.
* **Account Issues:** If we found the user had no Exchange Online license or their account was blocked for some reason (less likely specific to mobile, but possible), once that’s fixed (license assigned, block removed), their phone email works. This resolution is more of an account administration fix. Ensure to close the loop by verifying mobile sync.
* **Follow-up:** If the user got a new device, ensure they also transfer other needed settings (like contacts or 2FA apps) – not directly our issue, but related. Once mail is confirmed working, advise them on any company BYOD policy (like if they leave, IT might wipe corporate data). Closing the ticket, mention briefly what was done (e.g., “Re-added mail account on iPhone, sync restored”). If the problem tends to repeat (like with every password change), consider sending a tip to all users on how to update mobile email after a password change to preempt tickets.

## 15. Multi-Factor Authentication (MFA) Problems

**Description:** Many organizations use **MFA** for added security on accounts, particularly for Office 365 sign-ins or VPN. Users may face issues like not receiving the authentication code, their authenticator app not prompting, or being locked out because they got a new phone. These MFA snags are common support issues because if MFA fails, the user cannot log in at all. It’s critical to help them regain access while keeping security intact.

**Initial Questions:**

* What type of MFA are you using – do you get a text message code, a phone call, or do you use an authenticator app (like Microsoft Authenticator or Duo)?
* What part is failing? (e.g., “I didn’t get the text” or “the code from the app is not accepted” or “I can’t approve because I don’t have my old phone.”)
* Have you recently changed or reset your phone, phone number, or installed the authenticator on a new device? (This is often the cause – new phone means old app instance was lost.)
* Is there any alternative method you can try? (Many setups have a secondary method – perhaps they can try SMS instead of app, etc., if set up – asking this gauges if they have any access.)
* Are you completely locked out right now, or can you access some things? (Sometimes, if it’s just one service prompting MFA and they can skip it for now, but generally they’re blocked.)

**Troubleshooting Steps:**

1. **Identify Available Methods:** If the user has multiple MFA methods set (for example, both authenticator app and SMS), have them try the alternate. On the MFA prompt, usually there’s an option like “Sign in another way”. For instance, if the app isn’t working, try “text my mobile phone” if that was configured. This might let them in as a one-time workaround and also confirms whether one method specifically is broken.
2. **Time Sync (Authenticator App):** If the code from an authenticator app is being rejected, ensure the device’s time is correct. A common issue is the phone’s time drifted. In Microsoft Authenticator, there’s an option to sync time (for Google Authenticator too on Android). Usually enabling automatic time update on the phone and internet sync fixes code mismatch.
3. **Resend or Wait:** If it’s SMS or call and the user isn’t receiving it, have them check signal/reception. You can also attempt to “resend code.” Sometimes there’s just a delay. Also ask if they have any message-blocking or if their number changed. If their number changed, obviously the codes won’t go to the new number – then we know we must update it (with admin help).
4. **Temporary Bypass:** If the user is locked out due to lost device or similar, and you have the ability (or your admin does), you might set a temporary bypass for MFA. For example, Office 365 allows admins to disable MFA for a user or mark them as trusted temporarily. As Tier 1, you may not have that access, but you might reach out to someone who does. This would let the user in without MFA just once to at least get work done, while you fix MFA. Only do this with proper identity verification of the user to avoid social engineering risks.
5. **Reset MFA (Re-Enroll):** The typical solution for a lost or non-functioning MFA device is to reset the user’s MFA registration. In Azure AD/O365, an admin can hit “Require re-register MFA” or outright delete their current MFA methods. This will force the user to set up MFA from scratch. Do this when the user is ready – typically, they log in, it prompts to set up MFA as if new. You guide them through adding their authenticator app or phone number again.
6. **Guide Through Setup:** If the user has a new phone for the authenticator app, walk them through installing the app and scanning the QR code during setup. Make sure they have either the old phone number or email to get the initial code to start the process. If not, you have to rely on admin reset as above.
7. **Alternate Verification (if available):** Some systems have security questions or backup codes. If the user saved backup codes when they enrolled (few do, but worth asking), they can use one of those one-time codes to get in. Those codes can bypass MFA in an emergency. After using one, they usually should generate a new set.
8. **Account Lockout vs MFA:** Verify that it’s indeed an MFA issue and not an account lockout. If too many MFA attempts failed, sometimes systems temporarily lock the ability to do MFA – usually not, but just ensure the account itself isn’t locked in AD from password attempts (that’s separate and would show a different error).
9. **Test after Changes:** Once you or an admin updates the MFA settings (like enabling SMS on a new number or resetting MFA), have the user attempt login again and go through MFA enrollment or verification. Stay on call until they successfully authenticate. Also advise them to set up more than one method if possible (e.g., add both phone and app) to avoid future lockouts.
10. **Escalation:** If for some reason you cannot get around it (no admin rights, user has absolutely no way to verify), escalate to the security/identity team. They have higher powers or can verify identity through other means (maybe in-person ID check) and then disable MFA for the user temporarily. In extreme cases, they could provide a one-time bypass code. Keep the user informed; these cases are high priority because user can’t access anything.

**Common Resolutions & Next Steps:**

* **MFA Re-Registered:** The most frequent resolution is resetting the user’s MFA setup and having them enroll again. Once done, the user can log in normally, receiving prompts on their new device as expected. This is essentially starting fresh – problem resolved. Encourage the user to add a secondary MFA method (like adding a phone number if they primarily use an app) so there’s a backup in the future.
* **Phone Number Updated:** If the user changed their phone number and wasn’t getting codes, updating the system to the new number (via admin portal) solves it. After that, SMS codes arrive on their new phone and they can complete login. Lesson learned for user: always update your MFA info when you change numbers.
* **Alternate Method Used:** Sometimes, the user had another method and just didn’t know. For instance, they might have also set up their office phone as a backup and can use that. Once we guide them to “use another way” and they successfully login, we then fix their primary method. The issue is resolved with minimal downtime because of the backup. It’s a good practice to highlight – perhaps encourage them to keep multiple verification methods.
* **Temporary Bypass:** If we gave a one-day bypass (like disabled MFA for a day), the user can log in now. Then we ensure before re-enabling that they set up the new device. This is resolved but requires follow-up: make sure to re-secure the account after the bypass period. Typically, you wouldn’t close the ticket until MFA is fully operational again on their account.
* **Escalation/Security Team:** In rare complicated scenarios (maybe user is VIP and out of country without their device, etc.), the security team might use special procedures. Once they handle it, the user gains access. The resolution might be them using a different identity proof to authenticate. From a help desk perspective, document that proper security processes were followed and resolved. The user should now have MFA working (possibly on a new device). Close the ticket noting what was done (without exposing sensitive security details in the ticket, just “MFA reset per security admin” suffices).

## 16. OneDrive/SharePoint Sync Issues

**Description:** Users working with Office 365’s OneDrive or SharePoint (via **OneDrive Sync client**) may complain that files aren’t syncing. They might see a red X on the OneDrive icon, or certain files not updating to the cloud. Common issues include files stuck uploading, conflicts creating duplicate files, or “not signed in” errors. These “cloud drive” problems are frequent, as OneDrive is widely used for file backup and collaboration. Sync hiccups can be due to invalid file names, large files, or authentication glitches. (OneDrive issues are indeed a common problem with various causes.)

**Initial Questions:**

* Is the issue with your personal OneDrive (OneDrive for Business) or with a SharePoint/Teams library that you’ve synced? (This determines if it’s their individual files or a team site’s files.)
* What indicators do you see? (E.g., a red X or yellow exclamation on the OneDrive icon, or specific error messages in the OneDrive client?)
* Are all files failing to sync or just a specific file/folder? If specific, what’s special about those (very large file? file name contains special characters?)
* Did this start recently? Was it after a password change or perhaps after you got a new computer or re-login? (Could indicate an auth issue.)
* Have you tried signing into the Office apps or OneDrive app again? (Sometimes the session expires and just needs sign-in.)

**Troubleshooting Steps:**

1. **Check OneDrive Client Status:** Click the OneDrive icon in the taskbar (system tray). It often provides info on what’s wrong (“X files can’t be synced” or “Processing changes”). Note any error listed. Also check if the user is signed in – their name/email should show. If not signed in (maybe after a password change it signed out), sign in with their O365 account again and see if sync resumes.
2. **Pause and Resume Sync:** A quick fix that often clears a stuck sync is to Pause syncing (from the OneDrive menu) for a few minutes, then Resume. This sometimes flushes whatever was stuck.
3. **Identify Problem Files:** If OneDrive says certain files can’t sync, check those files. Common culprits:

   * File name too long or contains invalid characters (OneDrive has restrictions on characters like \* : < > etc.). Rename the file to a simpler name without special chars.
   * File size: OneDrive has an upload file size limit (around 250 GB currently, which is quite large; if somehow they tried something huge, note that).
   * If it’s a SharePoint library, maybe they exceeded the Item or Path limit. Unusual, but check if they have an extremely deep folder structure – simplifying might help.
   * If it’s a conflict (two versions of file), OneDrive usually creates a “-PC Name conflict” file. The user might need to manually merge or delete one.
4. **Ensure OneDrive Up to Date:** Have the user on the latest OneDrive client. You can get them to click “Settings > About > version”. If it’s outdated, downloading the latest OneDrive client from Microsoft and installing it can fix known bugs.
5. **Disk Space:** Check that the local disk isn’t full. If they’re trying to sync a lot, they need space to hold it. If disk is full, OneDrive will have trouble. Free up space if needed. Alternatively, they can use Files On-Demand so not everything downloads (OneDrive Settings > Files On-Demand).
6. **Sign Out/Sign In (OneDrive Reset Light):** If simpler steps fail, a common approach is to unlink and relink OneDrive. In OneDrive settings, choose “Unlink this PC”. This will stop syncing (doesn’t remove files, just breaks connection). Then sign in again and choose the location to sync. It will re-sync everything – that often resolves weird states. Warn the user that a full re-sync might take time if they have lots of data.
7. **OneDrive Full Reset (Heavy):** There is a command-line to fully reset OneDrive: `onedrive.exe /reset`. This wipes the sync cache. After running that, you usually have to start OneDrive again (search and open it) and then sign in fresh. Use this if the client is clearly malfunctioning.
8. **Check SharePoint Access:** If the issue is with a Teams/SharePoint library, ensure the user still has access to that site. If their permissions were removed, that could cause sync failures (although usually it just stops syncing new changes). To test, have them try to open the SharePoint site in a browser. If they can’t, that’s a permissions issue – possibly they lost access and need re-grant (escalate to site owner).
9. **Selective Sync:** Possibly a specific folder is problematic. You could disable sync for that folder (OneDrive settings > Account > Choose folders). Deselect the one causing trouble, let it remove, then maybe re-enable it after a while. This kind of “turn it off and on” for a particular subfolder sometimes helps.
10. **Escalation:** If large scale or persistent (like OneDrive is throwing errors that user home directory couldn’t be created, etc.), escalate to O365 support team. Sometimes tenant-level issues or a bug in that user’s OneDrive account might require Microsoft support. If the user’s data is at risk (e.g., they think some files didn’t upload and were deleted locally), definitely involve higher tier to attempt data recovery from cloud recycle bin or version history.

**Common Resolutions & Next Steps:**

* **File Rename or Removal:** Often, the resolution is finding that one offending file (like “Report:<2025>.docx” which has an invalid character). Renaming it to “Report-2025.docx” clears the error and OneDrive resumes syncing normally. The user’s sync icon turns blue (up-to-date). Ticket resolved; advise them on naming rules going forward to avoid this.
* **Re-authentication:** If the user was signed out (common after password change or if credentials expired), simply signing back in resolves all sync issues. All files sync up after re-login. This is resolved, and user should be good unless their password changes again – OneDrive usually prompts in that case.
* **OneDrive Re-link:** Unlinking/relinking the account often fixes weird unknown sync issues. After re-setup, user confirms that now all files are syncing and up-to-date. It might re-download some files. Instruct them to monitor and let you know if any specific file still doesn’t sync. Usually, if any remain, those are the problem files to address (we did that earlier though). If all green, ticket closed.
* **Space or Quota Adjustments:** If we found the user’s OneDrive was full (they hit their cloud storage quota), freeing up space or increasing quota resolves it. For example, user had 1TB filled, and the last files didn’t sync – we cleared some old files or advised to purchase more storage if needed (rare). Or if local disk was full, after cleanup, OneDrive can sync again. So environment issues resolved -> syncing resumes.
* **Handled via Escalation:** If the sync issue was due to something like a bug or needed a tenant setting (for instance, one user’s OneDrive was never provisioned correctly), Tier 2/3 might fix it behind the scenes (maybe re-provision the OneDrive site). Once done, the user sets it up and it works. So the resolution is technical but from user perspective, now it’s working. Document what was done if relevant for future reference.
* **Education:** Many sync issues are user-side, so a bit of education can prevent repeats. E.g., teach the user not to place Outlook PST files in OneDrive (they constantly change and don’t sync well), or avoid extremely deep folder structures. Mention that OneDrive status icons (the green check, blue sync, red x) give clues, and they can click them for info. Empowering the user might reduce future tickets.

## 17. Computer Will Not Power On

**Description:** A user says “My computer won’t turn on at all.” This is a hardware issue where the PC shows no signs of life – no lights, no fans, nothing on screen. It’s a common desk-side support ticket. Causes range from something as simple as an unplugged power cord (surprisingly common) to a failed power supply or battery. Tier 1’s job is to triage and attempt simple fixes, but often this may get handed to hardware support if parts need replacement. Nonetheless, basic troubleshooting can save a lot of time if it’s just a loose cable.

**Initial Questions:**

* Is this a **desktop or a laptop**? (Approach differs: desktops have cables and monitors, laptops have batteries.)
* What happens when you press the power button? (No lights or sounds at all? Any fan spinning? For laptops, any indicator LEDs when plugged in?)
* Has the PC been moved or bumped recently, or were there any electrical issues (like a power surge or outage) in the office/home?
* If a desktop: Is the monitor turning on (to ensure it’s not just a display issue)? Sometimes users say PC won’t turn on but it’s the monitor off. Check both.
* If a laptop: Is it charged? Have you tried plugging it in with the charger and waiting a few minutes? Any lights on the charger or laptop?

**Troubleshooting Steps:**

1. **Check Power Source:** Ensure the computer is actually receiving power. For a desktop, verify the power cable is firmly plugged into the PSU and the wall outlet (or power strip). If a power strip is used, is it turned on and not tripped? If possible, bypass surge protectors – plug directly into wall to test. For a laptop, check the AC adapter: is its LED on (if it has one)? Try a known-good outlet; sometimes outlets are dead or a power strip’s circuit breaker tripped.
2. **Swap/Reconnect Cables:** If another similar power cable is available (like the standard IEC cable for desktops or another laptop charger of same model), try that. The user might have a second monitor cable that fits the PC PSU – to test if the cable was bad. For laptop, if another compatible charger is around (same tip and voltage, ideally the same brand), see if that gets a charging light.
3. **Indicator Lights:** Observe any LEDs. Desktops often have a motherboard LED inside or a light on the front when power is present. Laptops usually have a charging or power LED. If absolutely no lights at all when plugged in, it’s likely a power issue (external or internal). If you see a charging light on a laptop but it still won’t power on, it might be something else (like a bad power button or motherboard).
4. **Reseat Connections (Desktop):** With the user’s permission (and if they’re comfortable or you/field tech present), check inside the desktop case – a loose power connector from PSU to motherboard could cause no power. Also ensure the voltage selector (if PSU has 115/230V switch) is correct for region (rare nowadays but if someone toggled it, the PSU won’t work). For SFF desktops, also check if the power button cable to the motherboard came loose.
5. **Battery and Residual Power (Laptop):** If it’s a laptop with a removable battery, try this: unplug the AC, remove the battery. Then hold the power button for \~15 seconds (discharge static). Then plug just AC back in (battery still out) and try to power on. This “flea power” drain can often reset internal hardware. If it turns on, you can later reinsert battery. If non-removable battery, holding power button for 15+ seconds while it’s unplugged can also force a reset on some devices.
6. **External Display Check:** If it’s possible the computer is on but the display is off (especially for laptops), ensure the brightness isn’t at zero or an external monitor toggle isn’t switched. For a desktop, listen for fans or drives spinning (if any signs of life but no display, it’s a different issue - possibly monitor or GPU). But given the user described “won’t power on”, likely it’s completely dead (no fan, etc.).
7. **Monitor vs PC (for “no display” cases):** Verify the monitor has power (power light on monitor) and the cable connections. Sometimes the PC is fine but the monitor or its cable is unplugged/turned off. Ask the user if they hear Windows startup sounds or see keyboard lights (NumLock toggling) – if yes, the PC is on, and it’s a monitor issue. If no signs from PC, then indeed PC isn’t powering.
8. **Replace Basic Components:** If Tier 1 has access to spare parts (often this becomes Tier 2), try a different power supply unit for a desktop if available, or a charged laptop battery/new charger for a laptop. This is usually beyond what a help desk call can do remotely, but if on-site, a quick test with a spare PSU or charger can confirm the component failure.
9. **Log and Escalate:** If none of the simple fixes work (and especially if it seems to be internal hardware failure like PSU or motherboard), escalate to the hardware support team. Provide what you’ve done (ensures they won’t just repeat the same steps). They likely will arrange for repair or replacement. If under warranty, they might contact vendor. In the meantime, consider providing the user a loaner machine so they can continue working.
10. **Data Access Plan:** A side note – if the PC is truly dead and going for repair, ask if the user urgently needs data from it. If the drive is intact and accessible, Tier 2 could pop it out and put it in an enclosure or temporarily in another PC to retrieve critical files. Or if they use OneDrive/SharePoint, most data might be in cloud anyway. It’s good to consider so the user isn’t blocked from important files while waiting for a fix.

**Common Resolutions & Next Steps:**

* **Plugged In Correctly:** It might sound silly, but a lot of times the fix is plugging the device in properly or turning on the power strip. Users sometimes accidentally kick out a plug under the desk. Once re-plugged, the PC powers on and boots normally. Ticket resolved – maybe with a gentle reminder to check cables, but also it’s an easy win.
* **Power Reset Worked:** Especially for laptops, the “press power for X seconds” trick often works. If the laptop then powers on, it could have been a firmware hang. The user should be back in Windows. Recommend they update their BIOS if it’s a known issue, or at least now they know the emergency procedure. Resolved, but if it repeats often, that laptop might need further hardware check.
* **Faulty Component Identified:** If by swapping a charger you found the original charger was dead (new one worked), then resolution is replacing the charger. The laptop now powers with the new charger. Provide a new adapter and close the ticket. Similarly, if a desktop’s PSU was suspected and Tier 2 replaced it, the PC works again – resolution: replaced power supply.
* **Hardware Repair/Replacement:** In cases where the machine itself had to be replaced (motherboard failure, etc.), resolution is delivering either a repaired PC or a new PC to the user. Once they have a working computer (with their data transferred if needed), the issue is resolved. Note the hardware action taken in the ticket.
* **Loaner and Follow-up:** If the user was given a temporary PC while theirs is fixed, that’s noted. The ticket might remain open until the original PC is repaired and returned. Once that’s done and user is fully back on their machine, then you close it out. Ensure everything is working on the returned machine (no other surprises).
* **Preventive Tip:** Advise the user to use a UPS or good surge protector especially if they are in an area with electrical issues (for desktops). For laptops, if they often drain battery fully, remind them to plug in – though modern laptops are fine being on battery. Not much preventive advice beyond general care. If the cause was something like “coffee spill last night” (which user may or may not admit), then the resolution might end up as a new device and advice on spill-proof keyboards, etc.

## 18. Monitor or Display Issues

**Description:** Display problems are a common hardware complaint. Examples: “My monitor won’t turn on,” “My second screen is not detected,” or “The display is flickering/no signal.” This can be due to the monitor being off, bad cables, incorrect display settings, or a faulty graphics output. Given many users have dual monitors, issues like one not working or wrong configuration arise often. These fall under hardware & peripheral breakdowns, but are usually straightforward to troubleshoot.

**Initial Questions:**

* How many monitors do you have, and which one is having the issue (primary/secondary)? Or is it a laptop built-in screen vs external monitor issue?
* What is the problem exactly – is the screen black/no signal, or is it displaying but, for example, at the wrong resolution or flickering?
* Has this monitor ever worked with your computer? (If it’s new, might be config. If it was working yesterday and not today, suspect cable or hardware change.)
* Did you recently change any display settings or physically move/rotate the monitors (could loosen cable)?
* If it’s an external monitor: is the power light on the monitor itself? (If no power light, it may be off or not getting power – separate from PC.)

**Troubleshooting Steps:**

1. **Power and Connections:** Check the monitor’s power first. Ensure the monitor’s power cable is plugged in and the monitor is turned on (many have a small power LED when on/standby). If no LED, try the power button. If still nothing, verify the outlet or power strip (plug something else to test, or use a different outlet). A surprisingly large number of monitor issues are just it being turned off or unplugged.
2. **Cable Check (Video Signal):** Determine what type of cable (HDMI, DisplayPort, DVI, VGA, USB-C) connects the monitor to the computer. Reseat the cable on both ends (monitor and PC). If possible, swap it with a known-good cable. Cables can go bad or get loose. If the monitor says “No signal,” that strongly indicates it’s not getting any input – hence focus on the connection or the source selection on monitor.
3. **Input Source on Monitor:** Many monitors with multiple inputs (HDMI1, HDMI2, DP, etc.) might be on the wrong input. Use the monitor’s menu buttons to select the correct input where the PC is connected. For example, if the PC is connected via HDMI, ensure the monitor is set to HDMI, not DP or VGA.
4. **Display Settings (Windows):** If this is about a second monitor not being recognized or extending properly: Right-click on desktop > Display Settings. Click “Detect” to see if Windows finds another display. If it does but it’s not displaying, it could be set to the wrong mode. Use Windows+P to cycle projection mode (Duplicate, Extend, etc.). Choose Extend to use dual monitors. Ensure the monitors are arranged correctly in settings and none is “disabled.”
5. **Graphics Driver/Application:** If the screen flickers or resolution is wrong, update the graphics driver. Also check if any recent driver update happened just before issue. Rolling back a driver is an option if a new update caused problems. For laptops with docking stations, update the dock’s firmware as well if external monitors act up.
6. **Swap Monitors/Ports:** To isolate if it’s the monitor or the PC output: if there are multiple outputs on the PC, try connecting the monitor to a different port (e.g., use HDMI instead of DP if available). Alternatively, if a second monitor is working, swap the cables between monitors. Does the issue stay with the monitor or stay with the port? For example, if monitor A had no signal on port 1, but when plugged into port 2 it works (and monitor B now fails on port 1), then port 1 (or that cable) is likely bad. If monitor A still fails on port 2 while monitor B works on port 1, monitor A might be bad.
7. **Laptop Specific:** If a laptop’s external display isn’t working, ensure the laptop is set to extend or duplicate (Windows+P). Some laptops require closing the lid or specific key combos (like Fn+F8 on older ones) to toggle display modes. Also check if the laptop’s own screen works – if not, perhaps the whole graphics output is down (which is more serious). If only external fails, could be a port issue or adapter (if using HDMI dongle, etc.). Try another adapter if one is involved.
8. **Resolution/Refresh Issues:** Sometimes a monitor can’t handle a certain resolution or refresh rate, resulting in no display. Boot into Safe Mode or use remote access to lower the resolution to something standard (like 1920x1080 at 60Hz) then reboot. This can bring a display back if previously set too high.
9. **Hardware Defect:** If after all these, one monitor still shows nothing and it’s not the PC/cable (because another monitor works in its place), that monitor could be dead. Look closely if the screen has any faint image (backlight issues) or any sign of power. If under warranty, arrange replacement. If not, user may need a new monitor. Similarly, if no video comes out of a specific PC port and others work, that port (or the graphics card for that output) might be defective. If it’s a desktop with multiple outputs on a discrete GPU, sometimes one port dies – using another is a fine workaround if enough remain. Otherwise, a new GPU might be needed (Tier 2 to handle).
10. **Escalation:** If it’s not resolved by these steps, escalate typically to a desktop support tech who can physically troubleshoot on-site with spare monitors, etc. Provide them info: e.g., “User’s second monitor not detected despite cable swap and working primary monitor; suspect bad HDMI port on laptop.” This primes them to bring maybe a dock or monitor to test.

**Common Resolutions & Next Steps:**

* **Cable/Power Fix:** The majority of display issues get resolved by plugging things in correctly. For instance, user had monitor off – turned it on, now it’s fine. Or the HDMI cable was loose – re-plug and picture is back. These quick fixes restore functionality immediately. Educate the user if needed (like how to use the monitor’s input menu or avoid jostling cables). Mark ticket resolved.
* **Corrected Settings:** If the user simply had it in the wrong mode (projection setting on “Second screen only” when no second screen present, etc.), pressing Windows+P to the right mode brings their display back. They’re usually quite relieved. Once they see their desktop on all monitors as expected, it’s resolved. Perhaps give a quick tutorial on Windows+P to manage multiple displays.
* **Monitor Replacement:** If a monitor is found to be bad (no power or no display even on different PCs), the solution is providing a new monitor. After swapping it out, the user has their screen working. That resolves the issue from the user’s perspective. Internally, you’d handle returning or repairing the bad monitor. Ticket closed with note “monitor was faulty, replaced with spare.”
* **Port Workaround:** If the PC has multiple outputs and one fails, often we just use another port (like use DisplayPort instead of HDMI). As long as the user can use their monitors normally, that’s a valid solution, albeit it might hint at a failing GPU. You might keep an eye on that PC. But if all is working, no immediate need to replace hardware – note the workaround in ticket in case someone else troubleshoots it later.
* **Dock/Adapter Issues:** If a docking station was not passing video and a reboot or firmware update fixed it, that’s resolved. Or if an adapter (like USB-C to HDMI dongle) was faulty and you replaced it, now monitors work – resolved via accessory replacement.
* **Escalation Outcome:** For complex multi-monitor setups (like 3-4 monitors) needing special config, Tier 2 might have had to configure display settings or special hardware. Once done, confirm with the user that all monitors are arranged and functioning as they want (e.g., correct primary monitor, resolutions correct). Then close the ticket.
* **User Guidance:** After resolution, sometimes users ask how to do certain things, like change which monitor is primary or how to pivot orientation. Show them in Display Settings how to rearrange monitors and choose primary, etc. A little training ensures they’re comfortable and might solve minor layout issues themselves in future.

## 19. Access or Permission Requests (Unable to Access Specific Resource)

**Description:** Often tickets are not about something “broken” but about a user needing access to a system or file they currently can’t reach. For example, “I need access to the Finance folder” or “I cannot open the HR database – permission denied.” This is a common managed IT task: managing user permissions and roles. Tier 1 frequently handles the initial request and coordinates approval and implementation. It’s important to follow company policy for granting access, since it ties into security.

**Initial Questions:**

* What exactly are you trying to access? (Get the precise name of the application, folder path, SharePoint site, distribution list, etc.)
* What happens when you try? Do you get a “access denied” message, login prompt, or you just don’t see the resource?
* Did you ever have access to this before, or is this a new requirement for your role? (If they had it before, it could have been removed inadvertently or due to role change. If new, it likely needs approval.)
* Is this time-sensitive for a task, or a general access request? (This helps prioritize – if they urgently need it to do their job, expedite as possible.)
* Who is the owner of this resource (if known)? For example, their manager or a department head often needs to approve access to confidential shares or apps.

**Troubleshooting/Process Steps:**

1. **Verify the Need:** Ensure the user actually requires this access and is allowed to have it. Often, this involves checking with their manager or the resource owner. Company policy might dictate approval steps. For instance, if a user in Sales wants access to an Accounting folder, you’d likely need Accounting manager approval. As Tier 1, gather the request details and forward for approval if you can’t approve it yourself.
2. **Check Current Permissions:** Look up the user’s groups/permissions related to that resource. In Active Directory, see if they are a member of the security group that grants access (if known). If it’s a SharePoint site, check if they are in the member list. Knowing the current state confirms they indeed lack access. Also see if others in their team have that access – sometimes new hires are simply not added to a necessary group.
3. **Determine How to Grant:** Identify the mechanism for access. For file shares, usually adding to an AD group or directly ACL. For an application, perhaps an admin portal where you assign a role. For distribution lists, adding them as a member. Each has a method. If it’s within Tier 1 rights to add and policy is satisfied (e.g., manager emailed approval), do it. If not, escalate the task to the proper admin team with details.
4. **Approval Documentation:** If required, get approval in writing (email or ticket comment from manager). This is important for audit trail since granting access often touches on security. If the user says “My manager told me I should have it,” ideally get an email from the manager or have the manager CC’d in the ticket granting permission.
5. **Implement Access:** Once authorized, proceed to grant access. Examples:

   * Add user to AD security group “FinanceFolder\_ReadWrite” using ADUC.
   * In SharePoint, go to site permissions and add the user (or better, add them to an appropriate SharePoint group if one exists).
   * For an application, create an account for them or assign a role/license (like adding to an Office 365 license or enabling a module for them).
   * If it’s about email lists, use Exchange Admin to add them to the distribution or shared mailbox permissions.
6. **Notify User of Completion:** Tell the user “You have been granted access to X. Please try again.” In some cases, it might require them to log off and on (if group membership token needed refresh) or wait a few minutes for changes to propagate. For file share, usually re-mapping or just trying again works if token updated. If urgent, have them log off and login to update group membership token.
7. **Test (if possible):** If you have access or can impersonate, you might test the access yourself to verify it works. But often not possible. So ask the user to test and confirm. For example, if it’s a folder, can they open it now? If an application, can they login now? This validation ensures the permission was applied correctly.
8. **Least Privilege Check:** Make sure to only give the access required. If they only need read, don’t give write unless that’s the only option. If a group exists for read vs write, choose appropriately. Also, avoid giving access to broader resources than needed (“I need one file, so I gave entire drive” – not ideal). Clarify scope with user if needed: maybe they need full folder, or maybe just a subfolder.
9. **Record Keeping:** Document the change either in the ticket or in your IAM system. Note which group or permission was granted and who approved. This is useful if later there’s a review or if the user changes roles and should be removed – IT can track who has what.
10. **Follow-Up/Review:** If access was temporary (sometimes users need something for a project), set a reminder to remove it later if applicable. Many times, though, it’s permanent until they leave or change jobs. Still, it’s good practice to periodically review permissions (often done by compliance teams annually – they might ask who approved and when, so your notes help).

**Common Resolutions & Next Steps:**

* **Access Granted:** The straightforward outcome is that after proper approval, the user is added to the appropriate group or given the needed permission. The user confirms “Yes, I can access it now, thank you.” Ticket closed as completed request. Ensure the ticket notes reflect who authorized it to cover compliance.
* **User Already Had Access (Resolved by Guidance):** Occasionally, the user might *have* access but was using the wrong method. For example, they might not know the correct path to a share or URL to a site. In such cases, the “fix” is telling them how to access it. E.g., “Use this network path \Server\Share, and here’s how to map it” or “Log into portal with your AD credentials.” If that resolves it, great. It was a training issue rather than permission. But you identified it by checking they were in the right group already. Close ticket with note “user guided on correct access procedure.”
* **Access Denied (Request Rejected):** Sometimes, upon seeking approval, it’s denied (“This user should not have that access”). In that case, inform the user politely that you cannot grant it and the request is declined by whoever. Offer any alternative solutions if applicable (maybe they can request certain data through another channel). Close the ticket as denied. This isn’t “resolved” in giving them what they want, but it’s completed from IT perspective. Document the denial (for record: “Access not authorized by \[Manager], user notified”).
* **Pending:** If waiting on approval or if it requires a change window (some orgs only do permission changes at certain times if on secure systems), keep the ticket open and inform the user of the timeline. The resolution happens once the change is implemented and user confirms access. If any delay, maintain communication. Once done, mark resolved.
* **Process Improvement:** If you see many similar requests (like every new hire in Dept X needs manual addition to Group Y), maybe raise a suggestion that onboarding procedures include that group assignment. This is outside the individual ticket, but worth noting to improve efficiency. Over time, mature organizations automate such access via role-based access control. Until then, Tier 1 will handle these one by one.

## 20. Virus/Malware Suspected on Computer

**Description:** A user might report “I have a virus” or “strange pop-ups are appearing” on their Windows PC. Alternatively, IT might be alerted to a machine behaving suspiciously. Virus and malware incidents are critical because they can lead to data loss or breaches. Common signs: frequent browser pop-ups, unknown programs asking for money (ransomware), computer extremely slow (if malware is using resources), or the antivirus detecting/quarantining threats. Tier 1 needs to respond quickly: contain and clean if possible, and know when to escalate to security specialists.

**Initial Questions:**

* What symptoms make you think it’s infected? (Pop-up ad? Redirected web pages? Ransom note? Unrecognized software running?)
* Did you recently download or run any file/email attachment before this started? (This might identify the source and type of malware.)
* Is the antivirus software (e.g., Windows Defender or company AV) giving alerts? If so, what exactly (names of threats, etc.)?
* Have you lost access to any files or noticed files encrypted/renamed? (Checking for ransomware signs.)
* Is your computer currently connected to the network? (If yes and it’s a severe infection, we might want them to disconnect to prevent spread.)

**Troubleshooting/Containment Steps:**

1. **Instruct User to Isolate (if severe):** If the machine seems heavily infected (ransomware note on screen, or it’s actively spreading – e.g., sending out spam), ask the user to **disconnect from the network** immediately. Unplug Ethernet or turn off Wi-Fi. This contains potential spread and protects data while you proceed. If it’s just mild adware, this may not be needed, but err on side of caution for anything suspicious.
2. **Run Antivirus Scan:** Guide the user to run a full system scan with the installed antivirus. For Windows Defender: Open Windows Security > Virus & threat protection > Scan Options > Full scan. This may take some time, but it will identify common malware and attempt removal. If the AV already popped up with detections, check the history to see if it removed them or needs action.
3. **Update Antivirus Definitions:** Ensure the AV is up-to-date. New malware requires updated definitions. If the PC is offline (due to step 1), you might skip update to focus on scanning with what it has, or temporarily allow network to update definitions then disconnect again. Up-to-date signatures improve detection.
4. **Identify the Malware (If Possible):** If the AV or user provided a malware name or symptom, do a quick knowledge search on it (if allowed in your role). For instance, if they say “there’s a popup saying Your computer is infected call this number,” that’s likely a scareware/adware, not actual virus – still, removal steps needed. If they saw something like “Cryptolocker” note, that’s ransomware – critical. Knowing what it is helps decide next steps (like ransomware = escalate to security immediately, attempt to isolate backups).
5. **Malware Removal Tools:** Sometimes the built-in AV might not catch everything. Depending on policy, you can use secondary scanners: e.g., Malwarebytes Free, or Microsoft Safety Scanner. If allowed, download on a clean PC and run on the infected one (since it may be offline, you might use a USB). These tools can find adware and PUPs that corporate AV might ignore. Run a scan and clean any found issues.
6. **Clear Browser/Temp Files:** If the issue is browser pop-ups or redirects, clear the browser cache and cookies. Check the browser extensions – remove any unfamiliar ones (common malware vector). Reset browser settings if needed. Also, clear temp files (using Disk Cleanup or Temp folder) – some malware hides there.
7. **Check Startup Programs:** Look at Task Manager > Startup or use MSCONFIG to see if any odd programs are set to run at startup. Malware often ensures it runs on boot. If you spot something suspicious (like “WebHelper” or gibberish .exe in AppData), disable it. Also check installed Programs in Control Panel for strange software names around the time issue started – uninstall those if they are clearly unwanted (toolbars, etc.).
8. **System Restore (if feasible):** If the infection started very recently and you have restore points, you might try restoring Windows to a point before infection. This can sometimes remove malware that attached itself after that date. Not always foolproof (some malware hide in restore points too), but an option.
9. **Assess Damage:** If scans find and remove threats, check the system’s behavior afterwards. Are the pop-ups gone? Does AV report clean now? Try opening a couple of programs and browser to ensure all good. If ransomware: if files are encrypted (with weird extension and won’t open), unfortunately cleaning the virus doesn’t restore files – that is a bigger issue to escalate (to see if backups exist).
10. **Escalation:** If the malware is of serious nature (ransomware, or something that might have stolen data like keylogger, or if you suspect a targeted attack), escalate to your cybersecurity incident response team. They may want forensic copies, might reimage the PC, and investigate how it got in. Also escalate if you cannot remove the malware fully – e.g., it keeps coming back, or scans can’t remove certain rootkits. They might use specialized tools or decide to wipe the system.

**Common Resolutions & Next Steps:**

* **Malware Removed by AV:** In many cases, running a full scan catches and removes the malware (e.g., it finds some trojans or adware and quarantines them). Once removed, the computer returns to normal function. The resolution: system cleaned. Recommend to user to be cautious with downloads/email. Possibly schedule a follow-up quick scan in a few days. Document which malware was removed (for trends).
* **Adware/Browser Cleaned:** If it was less an infection and more unwanted software (like toolbars or fake alerts), cleaning browser and using tools like Malwarebytes usually resolves it. The browser stops showing pop-ups, and user can work normally. Educate user: these often come from installing free software – avoid those or use custom install to deselect extras. If company has web filtering, maybe alert them to block the source site if needed.
* **No Malware Found (False Alarm):** Sometimes users might misinterpret something as a virus. For example, a weird browser pop-up might just be a bad website, not an infection. After scans show nothing and issue doesn’t recur, reassure them likely it was a scam website. In such case, resolution is educating them and perhaps tightening browser security (enable popup blocker, etc.). Mark ticket resolved with no infection found, user educated.
* **Ransomware or Major Incident:** If it was ransomware and files are encrypted, that’s a serious incident. Likely Tier 2/3 took over. The resolution might be: machine taken off network, wiped and rebuilt from backup, and files restored from last backup (if available). This often involves a separate process (incident report, etc.). The user gets a clean machine and as much data restored as possible. The ticket resolution would reference the incident handling. Also, password resets for user (in case credentials were compromised) and increased monitoring often follow. Communicate carefully with user about what happened and any data that could not be recovered.
* **Reimage as Last Resort:** If a machine was deeply infected or acting weird even after cleaning, sometimes IT decides to reimage (format and reinstall OS). This guarantees the virus is gone. It’s a heavy-handed but effective solution. Ensure user data is backed up (or was already in OneDrive etc.) before wipe. After reimage and reinstallation of necessary software, the user should verify everything is working and no sign of malware. This resolves the issue completely. Provide the user with guidance to prevent re-infection (maybe the source was a rogue USB or something – caution them).
* **Follow-up Actions:** Regardless of resolution, change the user’s passwords if there’s any chance malware captured them (especially for banking or corporate accounts). It’s good hygiene post-infection. Also, patch the system (make sure Windows and all apps are updated) because malware often exploits unpatched vulnerabilities. In summary, once clean, secure the system to prevent a repeat. Document the incident thoroughly in case it’s part of a broader attack pattern the security team is tracking.

---

**Sources:** The above guides are informed by common help desk practices and are backed by industry references. For instance, password resets are known to be a top ticket driver, and hardware issues like power cords and monitors are frequent with often simple fixes. Email and connectivity issues are high priority as noted in help desk trends. Virus attacks require swift action due to their impact. By following these troubleshooting steps and escalation points, a new technician can handle the most common Tier-1 support scenarios confidently and effectively.
