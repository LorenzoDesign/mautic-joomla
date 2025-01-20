Mautic-Joomla plugin
====================

This [Joomla](http://joomla.org) Plugin lets you add the [Mautic](http://mautic.org) tracking gif image to your Joomla website and embed Mautic forms in Joomla content. If you authorize this plugin as the Mautic API application, plugin will be able to push data from Joomla registration form to Mautic as a Lead.

This plugin is compatible with Joomla 4.4.10 and Mautic 5.2.1.

### Mautic Tracking Image

Tracking image works right after you enable the plugin, insert Base URL and save the plugin. That means it will insert 1 px gif image loaded from your Mautic instance. You can check HTML source code (CTRL + U) of your Joomla website to make sure the plugin works. You should be able to find something like this:

`<img src="http://yourmautic.com/mtracking.gif" />`

There will be probably longer URL query string at the end of the tracking image URL. It is encoded additional data about the page (title, url, referrer, language).

### Form embed

To embed a Mautic form into Joomla content, insert this code snippet:

	{mautic type="form" ID}

ID is the identifier of the Mautic form you want to embed. You can see the ID of the form in the URL of the form detail. For example for ```www.yourmautic.com/forms/view/1```, ID = 1.

### Dynamic content embed

To embed Mautic dynamic content, insert this code snippet:

    {mautic type="content" slot="slot_name"}Default content here.{/mautic}

slot_name is the identifier of the slot that you specified when adding a "Request dynamic content" decision to your campaign.

### Gated video embed

Mautic gated video supports YouTube, Vimeo, and MP4. If you want to use gated video on a Vimeo video, you must manually include the Froogaloop javascript. Also, to use gated video, you must manually include the jQuery javascript library on your page.

To embed Mautic gated videos, insert this code snippet:

    {mautic type="video" height="320" width="640" src="https://www.youtube.com/watch?v=QT6169rdMdk" gate-time="15" form-id="1"}
    
Note, you must replace the src with the URL to the video. Height and width attributes are optional, and will default to the values shown if omitted. Gate time is the time, in seconds, that you would like to pause the video and display the form that you entered as the form-id. 

### Plugin authorization

It is possible to send Lead data to Mautic via API only after authorization. You can create specific authorization API credentials for each application. To do that, go to your Mautic administration and follow these steps:

1. Go to Mautic Configuration / API Settings and set 'API enabled' to 'Yes', leave 'API mode' to 'OAuth1'. Save changes.
2. At the right-hand side menu where Configuration is should appear new menu item 'API Credentials'. Hit it.
3. Create new credential. Fill in 'Name' ('Joomla plugin' for example) and Callback URL to ```http://{yourJoomla.com}/administrator``` (change ```{yourJoomla.com}``` with actual URL of your Joomla instance). It goes to /administrator to make sure only Joomla admins can authorize Mautic plugin. Save credentials.
4. Mautic should generate 'Consumer Key' and 'Consumer Secret' key. Copy those two to Joomla plugin. Save the plugin. Hit the 'Authorize' button and follow instructions.

## Developer notes

If you want to add more Mautic features, submit PR to this plugin or use this plugin as a base and develop your own extension which could do more. mauticApiHelper.php class will configure the Mautic API for you.

### Release of the new version

This plugin uses Joomla Update Server which notifies the Joomla admin about availability of new versions of this plugin. To do that, update the version tag in mautic.xml in the master branch and then update version tag at updateserver.xml in the gh-pages branch accordingly.

[Current updateserver.xml](http://mautic.github.io/mautic-joomla/updateserver.xml)

### Integrate Mautic with another extension

To add Mautic lead generation to another extension is not hard at all. Just let the joomla amin install and configure this plugin then you can use Mautic API calls. Here is an example how to generate Mautic leads on any form save:

```php
// Include MauticApiHelper from the plugin 
require_once __DIR__ . '/mauticApiHelper.php';

$apiHelper  = new mauticApiHelper;
$leadApi    = \Mautic\MauticApi::getContext(
    "leads", 
    $apiHelper->getMauticAuth(), 
    $apiHelper->getMauticBaseUrl() . '/api/'
);

$lead = $leadApi->create(array(
    'ipAddress' => $_SERVER['REMOTE_ADDR'],
    'firstname' => $formData['firstname'],
    'lastname'  => $formData['lastname'],
    'email'     => $formData['email'],
));
```

More information about Mautic API calls can be found at [Mautic API Library](https://github.com/mautic/api-library) which is part of this plugin.
