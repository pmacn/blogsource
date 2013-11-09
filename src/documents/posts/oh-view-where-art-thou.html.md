```
type: post
layout: post
title: Oh view where art thou
date: 2013-11-06 09:30
tags: ['xunit','nancyfx']
```

I’ve quite recently started playing around with Nancy and was sitting down to write some tests for already existing code. (Yes, I sometimes write code before tests when exploring new frameworks) This code had already seen quite a lot of use and writing the initial tests was going to be easy.<!-- read more -->
 
Sure enough they all started out simple and working as intended one after the other. Making sure that SSL was forced where it was supposed to and that authentication was required for secured resources, etc.
 
But then I come to finally test something that returns an actual view.


``` cs
	[Fact]
	public void authorized_user_should_get_index_view()
	{
	    var identifier = GetIdentifier.ForExistingUser();
	    var browser = BuildBrowser();
	    var result = browser.Get("/", with => {
	        with.HttpsRequest();
	        with.FormsAuth(identifier , fakeAuthenticationConfiguration);
	    });
	    Assert.Equal(HttpStatusCode.OK, result.StatusCode);
	}
```

Lo and behold, I have a failing test for code that demonstrably works outside tests. 
Not only is it a failing test but IntelliTrace reveals that there was a **ViewNotFoundException** thrown internally for a view that works when running live.
 
After some digging I finally realize that I have two different issues going on.
 
- I had **not referenced my view engine assembly in my testing assembly**. Nancy.ViewEngines.Razor in this case since I quite like razor. Something you apparently have to do in order to test any of the non-default view engines.
- **Copy to Output Directory** was set to **Do not copy** for the view in question. Which seems to not be an issue for actually running the site but is if you want to write tests.

Adding the reference to the view engine assembly and changing the copy settings to **Copy when newer** or **Copy always** solves the problem and the test passes as expected.
