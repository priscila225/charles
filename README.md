# Charles Proxy Mobile Guide

## Intro

[Charles](https://www.charlesproxy.com/) is an HTTP proxy / HTTP monitor / Reverse Proxy that enables a developer to view all of the HTTP and SSL / HTTPS traffic between their machine and the Internet. This includes requests, responses and the HTTP headers (which contain the cookies and caching information).

When you enable charles you will stop your external internet accesss so it's expected to don't be able to open your PC browser and others connections in there. 
You are tunneling between your PC and your mobile device as a proxy

## Table of Contents

- [Charles Proxy Mobile Guide](#charles-proxy-mobile-guide)
  * [Intro](#intro)
  * [How to Install](#how-to-install)
    + [Android](#android)
      - [Android N (7.0, API level 24) and afterwards](#android-n--70--api-level-24--and-afterwards)
  * [How to get the information you want?](#how-to-get-the-information-you-want)
  * [How to import session?](#how-to-import-session)
  * [Common Questions & Bugs](#common-questions---bugs)
    + [Why I am not seeing the logs at all?](#why-i-am-not-seeing-the-logs-at-all)
    + [How to check if the port is in use?](#how-to-check-if-the-port-is-in-use)
    + [Still not seeing the logs and it's not the port or device proxy](#still-not-seeing-the-logs-and-its-not-the-port-or-device-proxy)

## How to Install

Note: If you update your PC OS or your mobile OS make sure you install the root certificate again 
The certificate needs to be updated to work properly. 

* **Charles** -> **Proxy** -> **SSL Proxying Settings...** -> **SSL Proxying**
    * Check "**Enable SSL Proxying**"
    * Add location matcher "**Host: &ast;**", "**Port: &ast;**"
    
    <img src="https://github.com/thyrlian/Charles-Proxy-Mobile-Guide/blob/master/Screenshots/macOS/SSL-Proxying-Settings.png" width="600">
    
* **Charles** -> **Help** -> **SSL Proxying** -> **Install Charles Root Certificate**
    * It would install the certificate to **Keychain**, and open it up automatically
    * Double click the certificate in Keychain
    * Expand **Trust**, select **Always Trust** for **When using this certificate**
    
    <img src="https://github.com/thyrlian/Charles-Proxy-Mobile-Guide/blob/master/Screenshots/macOS/Root-Certificate-not-trusted.png" width="400">
    
    <img src="https://github.com/thyrlian/Charles-Proxy-Mobile-Guide/blob/master/Screenshots/macOS/Root-Certificate-trusted.png" width="400">
    
* Get the **IP address** that Charles is listening to
    * **Charles** -> **Help** -> **Local IP Address**
    * Or get the WLAN IP address via command line (right into your clipboard)
        ```shell
        ifconfig | tr "\n" "→" | tr "\r" "→" | grep -Eo "→en0.*?→en[[:digit:]]" | grep -Eo "inet[[:blank:]+]([0-9]{1,3}\.){3}[0-9]{1,3}" | cut -d' ' -f2 | tr -d "\n" | pbcopy && pbpaste
        ```
    * Or get the LAN IP address via command line (right into your clipboard)
        ```shell
        ifconfig | tr "\n" "→" | tr "\r" "→" | grep -Eo "→en[[:digit:]].*?active→" | grep -v "en0" | grep -Eo "inet[[:blank:]+]([0-9]{1,3}\.){3}[0-9]{1,3}" | cut -d' ' -f2 | tr -d "\n" | pbcopy && pbpaste
        ```

### Android 

* Launch **Charles** and keep it running
* Get the **IP address**
* Make sure the Android device uses the same network as Charles
* On **Android** device
    * Go to **Settings** -> **Wi-Fi** -> long click the **network** in use -> **Modify network** -> **Advanced options** -> **Proxy** -> **Manual**
        * **Proxy hostname** = **IP address**
        * **Proxy port** = **8888**
        
        <img src="https://github.com/thyrlian/Charles-Proxy-Mobile-Guide/blob/master/Screenshots/Android/Wi-Fi.png" width="256">
    
    * Launch your mobile **Browser**, visit https://chls.pro/ssl, save the certificate
    
    <img src="https://github.com/thyrlian/Charles-Proxy-Mobile-Guide/blob/master/Screenshots/Android/certificate.png" width="256">
    
    * The system would ask you to set a lock screen **PIN** or **password**, just set one and save it
    * Now the certificate is installed
    * Open an application and monitor the traffic on Charles
* A dialog pops up on computer asking "A connection attempt to  Charles has been made from the host ...", just click **Allow** button

#### Android N (7.0, API level 24) and afterwards

* Open your Android project with Android Studio
* **Android Studio** -> **File** -> **New** -> **Android resource directory**
    * **Directory name** = **xml**
    * **Directory type** = **xml**
    * **Source set** = **debug**
* **Android Studio** -> **File** -> **New** -> **XML resource file**
    * **File name** = **network_security_config**
    * **Root element** = **network-security-config**
* Above step would generate a XML file with the given root element.  Now paste below content to replace the existing content in the generated XML file.
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <network-security-config xmlns:android="http://schemas.android.com/apk/res/android">
        <debug-overrides>
            <trust-anchors>
                <!-- Trust user added CAs while debuggable only -->
                <certificates src="user" />
            </trust-anchors>
        </debug-overrides>
    </network-security-config>
    ```
* Then go to **debug** source set, create a blank **AndroidManifest.xml** file if you don't have one for the debug build variant, and add content like below (eventually the manifest merger will merge it with the main manifest).  When you already have one, simply add the `networkSecurityConfig` attribute under `application`.
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools">
    
        <application
            android:networkSecurityConfig="@xml/network_security_config">
        </application>
    
    </manifest>
    ```

Now the SSL proxying should work for your app's debug build variant, but not for release build variant.

## How to get the information you want?

1. After all setup above to record a session you should enable the record button on charles UI.

<img src="https://github.com/priscila225/charles/blob/master/img/record.png" width="400">

2. Now open the debug app on your mobile device. **Navigate on app doing the steps provided for you.**
3.  Start to search what you need (command + F on mac or ctrl + F on windows) to make sure you session gets that. Example: if its an ad search by keyword "pubads" or something like that. If it's ads tag, provide the tags on search.. 
If its some url, provide part of the url and you will able to get it

<img src="https://github.com/priscila225/charles/blob/master/img/charles_search.png" width="400">

After that you can save your session

<img src="https://github.com/priscila225/charles/blob/master/img/save_session.png" width="400">

You can use this saved session to search what you want and have your internet back plus you can share this session of someone else.

## How to import session?

File -> Open session

<img src="https://github.com/priscila225/charles/blob/master/img/open_session.png" width="400">

## Common Questions & Bugs

### Why I am not seeing the logs at all? 

1. First : double check on your mobile device if the certificate is installed
2. Check if you set the proxy on mobile (sometimes we forgot to set it to match with charles)
3. Verify if the port you choose (default on charles is 8888) is not being used by another proccess (this occurs to me twice)
You can change the port for another value 

### How to check if the port is in use?
```shell
lsof -nP -iTCP:8888 | grep LISTEN
```
If the port is in use you can always change on Charles > Proxy > Proxy Settings -> set the value here

<img src="https://github.com/priscila225/charles/blob/master/img/port.png" width="600">

### Still not seeing the logs and it's not the port or device proxy
For some unknown reason sometimes the CA on charles change, it's rare but this happens. Some charles upgrade or another reason.
Remember to check CA again and go to install step on the CA part.
