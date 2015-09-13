Espresso-Intents enables validation and stubbing of Intents sent out by the application under test. It’s like [Mockito](http://mockito.org/), but for android intents.

# Prerequisites #

If you haven't done so, [set up Espresso and the Android Testing Support Library](https://code.google.com/p/android-test-kit/wiki/EspressoSetupInstructions).

# Download Espresso-Intents #

  * Open the **SDK manager** (in Android Studio it's in the Tools menu | Android | SDK Manager).
  * Make sure you have installed the **Android Support Repository** under Extras (espresso-intents 2.2 is available from rev 15).
  * Open your app's `build.gradle` file. This is usually not the top-level `build.gradle` file but `app/build.gradle`.

Add the following line inside dependencies:

```
    androidTestCompile 'com.android.support.test.espresso:espresso-intents:2.2'
```

Espresso-Intents is only compatible with Espresso 2.1+ and the testing support library 0.3 so make sure you update those lines as well:

```

    androidTestCompile 'com.android.support.test:runner:0.3'
    androidTestCompile 'com.android.support.test:rules:0.3'
    androidTestCompile 'com.android.support.test.espresso:espresso-core:2.2'
```

Note that the test runner in 0.1 used to be in a different artifact name (`com.android.support.test:support-lib:0.1`).

# IntentsTestRule #

Use `IntentsTestRule` instead of `ActivityTestRule` when using Espresso-Intents.

`IntentsTestRule` makes it easy to use Espresso-Intents APIs in functional UI tests. This class is an extension of `ActivityTestRule`, which initializes Espresso-Intents before each test annotated with `@Test` and releases Espresso-Intents after each test run. The activity will be terminated after each test and this rule can be used in the same way as `ActivityTestRule`.

# Intent validation #
Espresso-Intents records all intents that attempt to launch activities from the application under test. Using the intended API (cousin of **`Mockito.verify`**), you can assert that a given intent has been seen.

An example test that simply validates an outgoing intent:

```
   @Test
   public void validateIntentSentToPackage() {
     // User action that results in an external "phone" activity being launched.
     user.clickOnView(system.getView(R.id.callButton));
  
     // Using a canned RecordedIntentMatcher to validate that an intent resolving
     // to the "phone" activity has been sent.
     intended(toPackage("com.android.phone"));
   }
```


# Intent stubbing #
Using the intending API (cousin of **`Mockito.when`**), you can provide a response for activities that are launched with startActivityForResult (this is particularly useful for external activities since we cannot manipulate the user interface of an external activity nor control the `ActivityResult` returned to the activity under test):

An example test with intent stubbing:

```
   @Test
   public void activityResult_IsHandledProperly() {
     // Build a result to return when a particular activity is launched.
     Intent resultData = new Intent();
     String phoneNumber = "123-345-6789";
     resultData.putExtra("phone", phoneNumber);
     ActivityResult result = new ActivityResult(Activity.RESULT_OK, resultData);
  
     // Set up result stubbing when an intent sent to "contacts" is seen.
     intending(toPackage("com.android.contacts")).respondWith(result));
  
    // User action that results in "contacts" activity being launched.
    // Launching activity expects phoneNumber to be returned and displays it on the screen.
    onView(withId(R.id.pickButton)).perform(click());

    // Assert that data we set up above is shown.
    onView(withId(R.id.phoneNumber).check(matches(withText(phoneNumber)));
  }

```

# Intent matchers #
`intending` and `intended` methods take a hamcrest `Matcher<Intent>` as an argument. Hamcrest is library of matcher objects (also known as constraints or predicates). You have these options:
  * Use existing intent matcher: Easiest option, which should almost always be preferred.
  * Implement your own intent matcher: Most flexible option (see the section entitled "Writing custom matchers" in the [Hamcrest tutorial](http://code.google.com/p/hamcrest/wiki/Tutorial))

An example of intent validation with existing Intent matchers:
```

   intended(allOf(
        hasAction(equalTo(Intent.ACTION_VIEW)),
        hasCategories(hasItem(equalTo(Intent.CATEGORY_BROWSABLE))),
        hasData(hasHost(equalTo("www.google.com"))),
        hasExtras(allOf(
            hasEntry(equalTo("key1"), equalTo("value1")),
            hasEntry(equalTo("key2"), equalTo("value2")))),
        toPackage("com.android.browser")));
```