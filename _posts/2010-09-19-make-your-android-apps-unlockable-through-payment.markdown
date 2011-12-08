---
author: admin
date: '2010-09-19 11:22:28'
layout: post
slug: make-your-android-apps-unlockable-through-payment
status: publish
title: Make your Android apps unlockable through payment
wordpress_id: '607'
categories:
- Google Android
- Software Development
tags:
- android
- market
- pay
- payed
- paypal
- unlock
---

Do you want to offer a free version of your Android app and as well a payed
version without ads and more functionality but with the same basic features? I
wanted to do so with my SleepTimer app (extended/unlocked version with Widget
and no ads). But what can be problems to do so? Or what where problems that
kept me from doing until now?

  * Too much work to always update two apps (this was one of the biggest problems for me)
  * As a Swiss I can not sell apps on Android Market
  * Offering different ways to pay for unlocking (Google Checkout and PayPal)

Well. I found a solution, that got me around all those problems above!
Interested to hear? Continue reading. I will try to tell you enough on how to
do that for your apps. You can find a working example by searching for
SleepTimer.

**Two versions in one (unlock app functionality)** First let us toggle the problem of having two projects/apps to maintain.
The solution is to only have one and implement all the functionality in it.
And then we lock away the functionality for people who didn't pay. We set one
key (for example "is_app_payed") in SharedPreferences to default false. When
we have extended functionality we check if "is_app_payed" and if true, then we
offer the functionality. Or with apps, if payed, then we don't show them. Here
a quick example how I hide the "Unlock" menu point in the Activity after the
app has been paid:

{% highlight java %}

@Override
public boolean onPrepareOptionsMenu(Menu menu) {
	if(isAppPayed())
		menu.findItem(R.id.menu_unlock).setVisible(false);
	return super.onPrepareOptionsMenu(menu);
}

{% endhighlight %}

In the next points I will explain how to get money for the app and then
setting the "is_app_payed" in preferences to true.

**Ways to pay for the app**

There are three ways I am planning to offer for my app. 

  1. Browser PayPal (credit card / account): Donation over a donate button which links to the paypal app.
  2. PayPal in app payment (only with account possible): Download "Unlock app PayPal" in Android Market, pay through that app
  3. Purchase app (Google Checkout): Buy "Unlock app" in Android Market

I got the PayPal app done already and will publish it soon to the market. If
you need help with it, please write me. I might make another tutorial to
explain how to make in-app payments possible with PayPal.

To unlock the app with those ways, the original app needs  to discover the
apps installed and maybe get information from them. For that I made a menu
button "Unlock" which puts the user to the Activity which will check if
anything of the above has been done, and then either unlocks the app (sets
"is_app_payed" to true) or shows a screen with instructions and information.

**1. Browser PayPal Donate**

Here the user just does the payment through a donation button on the web, or a link in the app, that directs the user to the website to donate. You will receive the payment information and will have to do some work to help to get his app unlocked. Send the user the .apk file which is the same that other users buy in the Marked.

In the Unlock Activity of the original app, which can be reached through the
menu button, you check if the package name of the payed app exists. If yes,
then it sets "is_app_payed" to true and returns to the home screen. On the
home screen I changed the Logo to the payed logo, so the user knows, that the
app really is unlocked now.

Here some code out of my Unlock Activity to show how to detect if the other
app is installed:

{% highlight java %}

@Override
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);  
	setContentView(R.layout.unlock);  
	if(isAppPayed()){
		setAppToPayed();
		return;
	}  
	if (isPackageAvailable("ch.pboos.android.SleepTimerPayed")) {
		setAppToPayed();
		return;
	}  
	...
}  
private boolean isAppPayed() {
	SharedPreferences settings = PreferenceManager.getDefaultSharedPreferences(this);
        return settings.getBoolean(getString(R.string.attr_ispayed), false);
}  
public boolean isPackageAvailable(String packageName) {
	int sigMatch = getPackageManager().checkSignatures(getPackageName(), packageName);
	return sigMatch == PackageManager.SIGNATURE_MATCH;
}  
protected void setAppToPayed() {
	SharedPreferences settings = PreferenceManager
			.getDefaultSharedPreferences(this);
	SharedPreferences.Editor editor = settings.edit();
	editor.putBoolean(getString(R.string.attr_ispayed), true);
	editor.commit();
	finish();
}

{% endhighlight %}

I think the code should explain itself :). But note that the app which is
running this code and the unlock app need to have the same signature for the
check above.

**2. PayPal in app payment**

This one is a little bit more difficult. 1st you have to create an application that will make an in-app payment to your account over PayPal.  
This application you will put on the Android Market for free.

PayPal offers a [ PayPal SDK for
Android](https://www.x.com/community/ppx/xspaces/mobile/mep) which lets you
build this app. In the app all you do is launch the payment activity of PayPal
with the amount you want to get. When the amount is payed PayPal will tell
your app and you set a var in this app to remember that the user already payed
once through PayPal. Later this will get checked.

So that your original app can access this information there are different
ways. I chose to offer a Activity to check that information. That Activity
will be called through the original app through [startActivityForResult(...)](
http://developer.android.com/reference/android/app/Activity.html#startActivity
ForResult(android.content.Intent, int)) and will get back the result if it was
payed or not. The result can be immediate if it was payed. If it was not payed
yet, you can display the app itself.

Now to some code on how to do this:

In your original app, which should get unlocked, you do the following in
onCreate(...) to check and call the other app:

{% highlight java %}

if (isPackageAvailable("ch.pboos.android.SleepTimerPayPal")) {
	Intent intent = new Intent(
			"ch.pboos.android.SleepTimerPayPal.CHECK");
	startActivityForResult(intent, CHECK_PAYMENT);
}

{% endhighlight %}

The missing code can be found above.

In the PayPal app you have to do the following in your AndroidManifest.xml to
make the Activity, that checks the payment, available to your original
program:

You notice, that ch.pboos.android.SleepTimerPayPal.CHECK is called with the
Intent in the original app. So we need to make that Action lead us to the
exported activity PayedDialogActivity.

Now this is basically it for the PayPal way.

**3. Purchase app**

The app mentioned in 1. Browser PayPal Donate is the same app you can release on the Android Market. If they buy this app it will be installed and result in the same as if they got the app from you as explained above.  
I hope I was not too confusing. If you have any questions or suggestions,
please comment!

**Advanced Security (Make it more difficult to hack) velazcod mentioned in the comments, that this is really easy for users to hack. Might be a small percentage, but can influence your sales. Discussing with velazcod below and in chat we found a solution that is probably easiest and most effective. There is no code yet, but here the steps, that should tell you enough on what to do.**

  * User pays in some way and you register his Account ID or device ID. You save it to know who really paid.
  * In the app the user clicks on activate
  * opens a http/https request to your site for unlocking accountid or deviceid.
  * your server checks if the sent accountid/deviceid has paid
  * if yes, encrypt the accountid/deviceid with your private key and send the encrypted version back
  * the app saves the encrypted version in the preferences
  * to check if the app is payed you decrypt the value you saved in preferences and check if the decrypted version equals your accountid/deviceid.

Of course you have to include your public key in the app. And nobody can fake
the value in the preferences, because only you can do that with the private
key.

