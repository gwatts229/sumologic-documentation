---
id: manage-account
description: Update and manage your Sumo Logic account.
---

# Manage Your Account

You can review and update your account settings at any time through the **Preferences** page. 

## Update Your Email Address

As a Sumo Logic user, you can change your own email address as necessary.

1.  In Sumo Logic, click on your name and go to the **Preference** page.

    ![Change email](/img/get-started/change-email.png)

1.  Under **My Profile**, next to your **Username**, click **Change Email Address**.
1.  In the **Change Email Address** dialog, your current email address is
    displayed.
1.  Enter your **New Email**.
1.  Enter your **Current Password** for your Sumo Logic Account.
1.  Click **Submit**.
1.  The **Email Address Changed** dialog appears. An email with an activation link is sent to your new email address. Click the link in the email within seven days to complete your email address change, or this link will expire.

## Change Your Password

To change your password:

1.  Click your name in the Left Nav Bar and click **Preferences**.  
      
    ![Change Password](/img/get-started/change-password.png)
     
1.  Click **Change Password**.
1.  Enter your current password, and then enter the new password twice to verify it.  
      
    ![Change Password](/img/get-started/change-password2.png)

1.  Click **Submit** to finish resetting your password.

## Account Management in Preferences

The **Preferences** page contains settings that apply just to your account. Options on this page don't affect any other users in your organization. Find this page in the menu under your account name in the left nav of the Sumo Logic Web Application.

### My Profile

Under **My Profile**, the following information is displayed:

* **Organization ID** - Your Sumo Logic org ID
* **Username** - Your username is the email address associated with the account
* **Password** -The password you entered when activating your account. You can reset your password. 
* **Roles** - The Sumo Logic roles assigned to your user account

### My Security Settings

You can enable 2-Step Verification and view backup codes here. 

To set up 2-Step Verification you will need to install a Time-based One-Time Password (TOTP) application which will automatically generate an authentication code that changes after a certain period of time.

1.  Download one of the following apps:
    - For Android, iOS and Blackberry: [Google Authenticator](https://support.google.com/accounts/answer/1066447?hl=en)
    - For Android and iOS: [Duo Mobile](https://duo.com/product/trusted-users/two-factor-authentication/duo-mobile)
    - For Windows Phone: [Authenticator](https://www.microsoft.com/en-us/store/p/authenticator/9wzdncrfj3rj)
1.  Scan the QR code displayed on your screen with your TOTP App.
1.  After the TOTP App is configured, enter two consecutive authentication codes.

### My Access Keys

Users with a role that grants the **Create Access Keys** capability can create and manage their own Access Keys. For more information see [Access Keys].

### My Preferences

Preference settings are only changed for your personal account; they do not affect any other users in your organization. Any changes you make to your preferences take effect the next time you sign in, not during the current session.

- **Default Timezone.** If you want the Sumo Logic user interface to use your local time zone, or a time zone different from the time zone used in the timestamp of your log messages, change the setting here. This is a personal setting, and does not change the time zone for anyone else in your organization. This option overrides the timezone set in your web browser, and affects all hours and minutes displayed in the user interface, including time ranges on the Search page, the Time column in the Messages pane, and in Dashboards. It does not affect the configurations of previously created Scheduled Searches or Real Time Alerts. For more information, see [Timestamps, Time Zones, Time Ranges, and Date Formats].
- **Always show the timezone offset in displayed timestamps.** This setting is on by default. To not show the timezone offset in displayed timestamps, deactivate this check box. 
- **Date Format:** Select from the following international date format options:
    * Use the browser's default date format.
    * MM/DD/YYYY (04/22/2015)
    * DD/MM/YYYY (22/04/2015
    * YYYY/MM/DD (2015/04/22)

    :::warning
    If you change the date format option, it will affect your saved searches in the Library. Any saved searches that use absolute dates for their time range must be modified to use the new date format. Scheduled searches will continue to run properly. You would need to modify the date format only if you rescheduled the search.
    :::

- **Web session timeout.** Choose an option to set the length of time before your Sumo Logic session times out. Options include 5 minutes to 7 days. For information on web session timeouts and Multi-account Access, see [Multi-account Access.].
- **Receive email notifications whenever content is shared with you**. 
- **Enable keyboard shortcuts.** Web Application and [keyboard shortcuts] are enabled by default. Press ? to see the list of shortcuts. To disable keyboard shortcuts, for example, if they conflict with an international keyboard, deselect the check box. 

  :::note
  Keyboard shortcuts are disabled when typing in the [search text box]. 
  :::

- **Automatically run the search after selecting it from a list of saved searches.** Keep this option selected if you'd like to run a saved search as soon as you select it. Deselect the option if you would like to start the search manually.
- **Show confirmation dialog when closing a search tab.** On the Search page, if you want to be prompted with a confirmation dialog before you can close a search tab, select this check box.
- **Automatically open the search autocomplete dialog when editing. Use `<Esc>` or `<Alt>` `<Space>` to open it manually).** Keep this option selected if you'd like to open the [search autocomplete] dialog when you are editing a query. Deselect the option to disable the search autocomplete dialog. 
- **On login, reopen the tabs from your previous session**. If you want to reopen the tabs from your previous session when you login to a new session, select this checkbox.
- **Log Search Query editing.** Select one of the following options:

    - `<Enter>` runs the query, `<Alt>` `<Enter>` creates a new line.
    - `<Alt>` `<Enter>` runs the query, `<Enter>` creates a new line.

If you have changed any settings, click **Save**.