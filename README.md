# ehow-ow-profile-loader



OpenWrap Profile tag looks like below. This code helps publishers to load the profile code in staging environment to test on a given browser using debug query params (in address bar) `pwtv=22` where `pwtv` is the version number of the profile to be loaded on the page.

```
<script type="text/javascript">
(function() {
	var purl = window.location.href;
	var url = '//ads.pubmatic.com/AdServer/js/pwt/160687/6643';
	var profileVersionId = '';
	if(purl.indexOf('pwtv=')>0){
		var regexp = /pwtv=(.*?)(&|$)/g;
		var matches = regexp.exec(purl);
		if(matches.length >= 2 && matches[1].length > 0){
			profileVersionId = '/'+matches[1];
		}
	}
	var wtads = document.createElement('script');
	wtads.async = true;
	wtads.type = 'text/javascript';
	wtads.src = url+profileVersionId+'/pwt.js';
	var node = document.getElementsByTagName('script')[0];
	node.parentNode.insertBefore(wtads, node);
})();
</script>
```

ehow.com does not use the above given tag but loads the `pwt.js` through `ehow.js` as following, due to the below code we are unable to run a staging version of a profile to debug

```
"openWrap": {
    "desktopUrl": "//ads.pubmatic.com/AdServer/js/pwt/160687/6643/pwt.js",
    "mobileUrl": "//ads.pubmatic.com/AdServer/js/pwt/160687/6644/pwt.js"
},
```

Below function will generate the OpenWrap Profile code URL for a provided `profileId`. The function also supports generating a Profile code URL when debugging params are added in page URL (address bar).

```
function generateOpenWrapProfileCodeURL(profileId){
    var purl = window.location.href;
    var url = 'https://ads.pubmatic.com/AdServer/js/pwt/160687/'+profileId;
    var profileVersionId = '';
    if(purl.indexOf('pwtv=')>0){
        var regexp = /pwtv=(.*?)(&|$)/g;
        var matches = regexp.exec(purl);
        if(matches.length >= 2 && matches[1].length > 0){
            profileVersionId = '/'+matches[1];
        }
    }
    return url+profileVersionId+'/pwt.js';
}
```

Can be used as :
```
generateOpenWrapProfileCodeURL(6643)
// https://ads.pubmatic.com/AdServer/js/pwt/160687/6643/pwt.js

generateOpenWrapProfileCodeURL(6644)
// https://ads.pubmatic.com/AdServer/js/pwt/160687/6644/pwt.js

```

The function `generateOpenWrapProfileCodeURL` when included in the `ehow.js` can be used to replace the existing profile loading code as following

Existing code in `ehow.js`:
```
"openWrap": {
    "desktopUrl": "//ads.pubmatic.com/AdServer/js/pwt/160687/6643/pwt.js",
    "mobileUrl": "//ads.pubmatic.com/AdServer/js/pwt/160687/6644/pwt.js"
},
```

To be modified as...
```
"openWrap": {
    "desktopUrl": generateOpenWrapProfileCodeURL(6643),
    "mobileUrl": generateOpenWrapProfileCodeURL(6644)
},
```
