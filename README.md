# SkyAuth-CPP-Example

SkyAuth C++ example for the https://skyproject.cc authentication system.

## **Bugs**

If the default example not added to your software isn't functioning how it should, please report a bug here https://skyproject.cc/app/?page=support

## **Security practices**

* Utilize obfuscation provided by companies such as VMProtect or Themida (utilize their SDKs too for greater protection)
* Preform frequent integrity checks to ensure the memory of the program has not been modified
* Don't write the bytes of a file you've downloaded to disk if you don't want that file to be retrieved by the user. Rather, execute the file in memory and erase it from memory the moment execution finishes

*SkyAuth is provided in Source Code Form. The burden of client-side protection is on you the software developer, as it would be with any authentication system.*

## **`SkyAuthApp` instance definition**

Visit https://skyproject.cc/app/ and select your application, then click on the **C++** tab

```cpp
std::string name = "example"; // application name. right above the blurred text aka the secret on the licenses tab among other tabs
std::string ownerid = "JjPMBVlIOd"; // ownerid, found in account settings. click your profile picture on top right of dashboard and then account settings.
std::string secret = "db40d586f4b189e04e5c18c3c94b7e72221be3f6551995adc05236948d1762bc"; // app secret, the blurred text on licenses tab and other tabs
std::string version = "1.0"; // leave alone unless you've changed version on website
std::string url = "https://SkyAuth.win/api/1.2/"; // change if you're self-hosting

api SkyAuthApp(name, ownerid, secret, version, url);
```

## **Initialize application**

You must call this function prior to using any other SkyAuth function. Otherwise the other SkyAuth function won't work.

```cpp
SkyAuthApp.init();
if (!SkyAuthApp.data.success)
{
	std::cout << skCrypt("\n Status: ") << SkyAuthApp.data.message;
	Sleep(1500);
	exit(0);
}
```

## **Display application information**

```cpp
SkyAuthApp.fetchstats();
std::cout << skCrypt("\n\n Number of users: ") << SkyAuthApp.data.numUsers;
std::cout << skCrypt("\n Number of online users: ") << SkyAuthApp.data.numOnlineUsers;
std::cout << skCrypt("\n Number of keys: ") << SkyAuthApp.data.numKeys;
std::cout << skCrypt("\n Application Version: ") << SkyAuthApp.data.version;
std::cout << skCrypt("\n Customer panel link: ") << SkyAuthApp.data.customerPanelLink;
```

## **Check session validation**

Use this to see if the user is logged in or not.

```cpp
std::cout << skCrypt("\n Checking session validation status (remove this if causing your loader to be slow)");
SkyAuthApp.check();
std::cout << skCrypt("\n Current Session Validation Status: ") << SkyAuthApp.data.message;
```

## **Check blacklist status**

Check if HWID or IP Address is blacklisted. You can add this if you want, just to make sure nobody can open your program for less than a second if they're blacklisted. Though, if you don't mind a blacklisted user having the program for a few seconds until they try to login and register, and you care about having the quickest program for your users, you shouldn't use this function then. If a blacklisted user tries to login/register, the SkyAuth server will check if they're blacklisted and deny entry if so. So the check blacklist function is just auxiliary function that's optional.

```cpp
if (SkyAuthApp.checkblack()) {
	abort();
}
```

## **Login with username/password**

```cpp
std::string username;
std::string password;
std::cout << skCrypt("\n\n Enter username: ");
std::cin >> username;
std::cout << skCrypt("\n Enter password: ");
std::cin >> password;
SkyAuthApp.login(username, password);
if (!SkyAuthApp.data.success)
{
	std::cout << skCrypt("\n Status: ") << SkyAuthApp.data.message;
	Sleep(1500);
	exit(0);
}
```

## **Register with username/password/key**

```cpp
std::string username;
std::string password;
std::string key;
std::cout << skCrypt("\n\n Enter username: ");
std::cin >> username;
std::cout << skCrypt("\n Enter password: ");
std::cin >> password;
std::cout << skCrypt("\n Enter license: ");
std::cin >> key;
SkyAuthApp.regstr(username, password, key);
if (!SkyAuthApp.data.success)
{
	std::cout << skCrypt("\n Status: ") << SkyAuthApp.data.message;
	Sleep(1500);
	exit(0);
}
```

## **Upgrade user username/key**

Used so the user can add extra time to their account by claiming new key.

> **Warning**
> No password is needed to upgrade account. So, unlike login, register, and license functions - you should **not** log user in after successful upgrade.

```cpp
std::string username;
std::string key;
std::cout << skCrypt("\n\n Enter username: ");
std::cin >> username;
std::cout << skCrypt("\n Enter license: ");
std::cin >> key;
SkyAuthApp.upgrade(username, key);
```

## **Login with just license key**

Users can use this function if their license key has never been used before, and if it has been used before. So if you plan to just allow users to use keys, you can remove the login and register functions from your code.

```cpp
std::string key;
std::cout << skCrypt("\n Enter license: ");
std::cin >> key;
SkyAuthApp.license(key);
if (!SkyAuthApp.data.success)
{
	std::cout << skCrypt("\n Status: ") << SkyAuthApp.data.message;
	Sleep(1500);
	exit(0);
}
```

## **Login with web loader**

```cpp
std::cout << "\n Waiting for user to login";
SkyAuthApp.web_login();
std::cout << "\n Waiting for button to be clicked";
SkyAuthApp.button("close");
```

## **User Data**

Show information for current logged-in user.

```cpp
std::cout << skCrypt("\n User data:");
std::cout << skCrypt("\n Username: ") << SkyAuthApp.data.username;
std::cout << skCrypt("\n IP address: ") << SkyAuthApp.data.ip;
std::cout << skCrypt("\n Hardware-Id: ") << SkyAuthApp.data.hwid;
std::cout << skCrypt("\n Create date: ") << tm_to_readable_time(timet_to_tm(string_to_timet(SkyAuthApp.data.createdate)));
std::cout << skCrypt("\n Last login: ") << tm_to_readable_time(timet_to_tm(string_to_timet(SkyAuthApp.data.lastlogin)));
std::cout << skCrypt("\n Subscription name(s): ");
std::string subs;
for (std::string value : SkyAuthApp.data.subscriptions)subs += value + " ";
std::cout << subs;
std::cout << skCrypt("\n Subscription expiry: ") << tm_to_readable_time(timet_to_tm(string_to_timet(SkyAuthApp.data.expiry)));
```

## **Check subscription name of user**

If you want to wall off parts of your app to only certain users, you can have multiple subscriptions with different names. Then, when you create licenses that correspond to the level of that subscription, users who use those licenses will get a subscription with the name of the subscription that corresponds to the level of the license key they used.

```cpp
for (std::string subs : SkyAuthApp.data.subscriptions)
{
	if (subs == "default")
	{
		std::cout << skCrypt("\n User has subscription with name: default");
	}
}
```

## **Application variables**

A string that is kept on the server-side of SkyAuth. On the dashboard you can choose for each variable to be authenticated (only logged in users can access), or not authenticated (any user can access before login). These are global and static for all users, unlike User Variables which will be dicussed below this section.

```cpp
// get data from global variable with name 'status'
std::cout << "\n status - " + SkyAuthApp.var("status");
```

## **User Variables**

User variables are strings kept on the server-side of SkyAuth. They are specific to users. They can be set on Dashboard in the Users tab, via SellerAPI, or via your loader using the code below. `discord` is the user variable name you fetch the user variable by. `test#0001` is the variable data you get when fetching the user variable.

```cpp
std::cout << "\n user variable - " + SkyAuthApp.getvar("discord"); // get value of the user variable 'discord'
```

And here's how you fetch the user variable:

```cpp
SkyAuthApp.setvar("discord", "test#0001"); // set the value of user variable 'discord' to 'test#0001'
```

## **Application Logs**

Can be used to log data. Good for anti-debug alerts and maybe error debugging. If you set Discord webhook in the app settings of the Dashboard, it will send log messages to your Discord webhook rather than store them on site. It's recommended that you set Discord webhook, as logs on site are deleted 1 month after being sent.

You can use the log function before login & after login.

```cpp
SkyAuthApp.log("user logged in"); // send event to logs. if you set discord webhook in app settings, it will send there instead of dashboard
```

## **Ban the user**

Ban the user and blacklist their HWID and IP Address. Good function to call upon if you use anti-debug and have detected an intrusion attempt.

Function only works after login.

```cpp
SkyAuthApp.ban();
```

## **Ban the user (with reason)**

Ban the user and blacklist their HWID and IP Address. Good function to call upon if you use anti-debug and have detected an intrusion attempt.

Function only works after login.

The reason paramater will be the ban reason displayed to the user if they try to login, and visible on the SkyAuth dashboard.

```cpp
SkyAuthApp.ban("Don't try to crack my loader, cunt.");
```

## **Server-sided webhooks**

Send HTTP requests to URLs securely without leaking the URL in your application. You should definitely use if you want to send requests to SellerAPI from your application, otherwise if you don't use you'll be leaking your seller key to everyone. And then someone can mess up your application.

```cpp
std::string resp = SkyAuthApp.webhook("Sh1j25S5iX", "&mak=best&debug=1");
if (!SkyAuthApp.data.success) // check whether webhook request sent correctly
{
	std::cout << skCrypt("\n\n Status: ") << SkyAuthApp.data.message;
	Sleep(1500);
	exit(0);
}
std::cout << "\n Response recieved from webhook request: " + resp;
```

## **Download file**

`362905` is the file ID you get from the dashboard after adding file.

```cpp
// remember, certain paths like windows folder will require you to turn on auto run as admin https://stackoverflow.com/a/19617989
std::vector<std::uint8_t> bytes = SkyAuthApp.download("362905");
if (!SkyAuthApp.data.success) // check whether file downloaded correctly
{
	std::cout << skCrypt("\n\n Status: ") << SkyAuthApp.data.message;
	Sleep(1500);
	exit(0);
}
std::ofstream file("file.dll", std::ios_base::out | std::ios_base::binary);
file.write((char*)bytes.data(), bytes.size());
file.close();
```

## **Chat channels**

Allow users to communicate amongst themselves in your program.

```cpp
SkyAuthApp.chatget("test");
for (int i = 0; i < SkyAuthApp.data.channeldata.size(); i++)
{
	std::cout << "\n Author:" + SkyAuthApp.data.channeldata[i].author + " | Message:" + SkyAuthApp.data.channeldata[i].message + " | Send Time:" + tm_to_readable_time(timet_to_tm(string_to_timet(SkyAuthApp.data.channeldata[i].timestamp)));
}
```

```cpp
std::cout << skCrypt("\n Type Chat message: ");
std::string message;
std::getline(std::cin, message);
if (!SkyAuthApp.chatsend("test", message))
{
	std::cout << SkyAuthApp.data.message << std::endl;
}
```

## **Changing username**

Allow users to change their username when logged-in.

```cpp
std::cout << skCrypt("\n Change Username To: ");
std::string newusername;
std::cin >> newusername;
SkyAuthApp.changeusername(newusername);
if (SkyAuthApp.data.success) 
{
        std::cout << SkyAuthApp.data.message << std::endl;
}
```
