# _**Watson Assistant Lab 4**_: Understanding User Sentiment - Integrating Watson Natural Language Understanding
In this lab we're going to show how you can extend your chatbot by using additional AI capabilities. We'll use the _**Watson Natural Language Understanding (NLU)**_ service to quantify _**sentiment**_ of user responses, and integrate this with _**Watson Assistant**_ using _**IBM Cloud Functions**_.

In practical terms, we'll build another _**Watson Assistant**_ _intent_, that allows the user to submit a review of a mobile phone. When we pick up this _intent_, we'll ask the user for a phone brand and the content of their review, feed the review text through _**Watson NLU**_ to check the sentiment, and return a response based on its positivity/negativity.

## Requirements
- Successful completion of [Lab 3: Enhancing Your Chatbot with Advanced Watson Assistant Features](../3-Advanced).

## Agenda
- Introduction to _**Watson NLU**_
- Setup a _**Watson NLU**_ instance and an _**IBM Cloud Function**_
- Setup _**Watson Assistant**_ to use _**IBM Cloud Functions**_
- Create `Submit Review` _intent_ and _dialog_

## Introduction to _**Watson NLU**_
_**Watson Natural Language Understanding**_ is a collection of APIs that offer text analysis through _natural language processing_. This set of APIs can analyse text to help you understand its _concepts_, _entities_, _keywords_, _sentiment_, and more. You can also create custom models to get specific results that are tailored to your industry/domain.

You can apply _**Watson NLU**_ to various use cases, including content recommendation, advertising optimisation, audience segmentation, data mining, and the one we will use here, voice-of-customer analysis.

**(1)** To get a feel for what _**Watson NLU**_ can do, have a look the demo application [here](https://natural-language-understanding-demo.ng.bluemix.net/).

![](./images/01-watson-nlu-demo.jpg)

**(2)** The demo allows you to perform natural language processing analysis on any text or URL that you supply. If you hit `Analyze` using the text provided, you'll be presented with an analysis of the article, showing:

Analysis       | Description
---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Sentiment      | The general sentiment of your content
Emotion        | Detects anger, disgust, fear, joy, or sadness that is conveyed in the content
Keywords       | Returns important keywords in the content
Entities       | Extracts people, companies, organisations, cities, geographic features, and other information from the content
Categories     | Classifies content into a hierarchy that's five levels deep
Concepts       | Returns high-level concepts in the content. For example, a research paper about deep learning might return the concept, "Artificial Intelligence" although the term is not mentioned
Semantic Roles | Parses sentences into subject, action, and object form

Here are the _**entities**_ extracted from the text:

![](./images/02-demo-entities.jpg)

And here are the _**emotion**_ and _**sentiment**_ scores:

![](./images/03-demo-emotion.jpg)

![](./images/04-demo-sentiment.jpg)

Both of these can be extremely useful to us when building chatbots. For example, if we can analyse a user's responses in real-time so we can understand when they are getting angry, we could automatically route the user to a human operator, or start to use different tones/language.

In our case, we are just going to use the sentiment data to 'grade' the sentiment of a review, and provide a customised response.

## Setup a _**Watson NLU**_ instance and an _**IBM Cloud Function**_
**(1)**	First create a _**Watson NLU**_ instance in IBM Cloud, if you don't already have one. Once more give it a unique name - something that includes your initials will help you find it later.

![](./images/05-create-nlu-service.jpg)

![](./images/06-create-nlu-service2.jpg)

**(2)** Once your service is created, click `Manage`. We'll need the `API key` and the `URL` from this page to access the service later, so copy them both from here and paste them to a temporary file or note. You can copy the `API Key` by clicking the `Copy to clipboard!` icon.

![](./images/13-nlu-credentials.jpg)

**(3)** Next, we need a mechanism by which we can call the NLU service, so we can passing our user's review text to the API and return a sentiment score. We'll do this by creating an _**IBM Cloud Function**_.

With _**IBM Cloud Functions**_ you can write lightweight code that executes application logic in a scalable way. You can then run this code on-demand via requests from applications like our _**Watson Assistant**_ chatbot, or automatically in response to events.

Some examples of how we might use an _**IBM Cloud Function**_ from within _**Watson Assistant**_ include:

- Validating information that you collect from the user.
- Doing calculations or string manipulations on user input that are too complex for supported _SpEL_ expression methods to handle.
- Interacting with an external web service to get information. For example, you might check on the expected arrival time for a flight from an air traffic service.
- Sending requests to an external application, such as a restaurant reservation site, to complete a simple transaction on the user's behalf.

As you've already seen, _**Watson Assistant**_ is great at collecting the required information from a user before returning an appropriate response. So for example, a chatbot user might request a weather forecast and complete _slots_ for required information like _location_ and _date_. We could then call an _**IBM Cloud Function**_ that passes the _location_ and _date_ to a weather forecasting service such as [The Weather Company](https://cloud.ibm.com/catalog/services/weather-company-data), and return the forecast data to _**Watson Assistant**_ so it can present it to the user.

![](./images/07-cloud-function-example.jpg)

**(3)** Fortunately, most of the web services we might want to take advantage of have published and documented API calls, so building an _**IBM Cloud Function**_ to call them is usually relatively straightforward, involving some small tweaks to sample code.

IBM Watson service calls are documented [here](https://github.com/watson-developer-cloud) for a number of different programming languages. We're going to use _Javascript_ (as we'll also use it in later labs) - you can take a look at the documentation and templates we've modelled the calls on [here](https://github.com/watson-developer-cloud/node-sdk) if you wish, although we'll supply all of the code you need to complete the lab.

**(4)** Go to _**IBM Cloud Functions**_ by selecting the `burger icon` in the top left-hand corner, then `Functions`.

![](./images/08-find-functions.jpg)

From there, first _**check the pulldown menu at the top right**_.

- If you are using your **own** IBM Cloud account, select the option from the menu that matches the location you have been using to create your existing services (you can check this [here](https://cloud.ibm.com/resources)).

- If you are using a **shared** IBM Cloud account, select the option that reflects the namespace and location you have been provided.

Next, click `Start Creating`, then `Create Action`.

![](./images/09-functions-start-creating.jpg)

![](./images/10-create-action.jpg)

**(5)** Call your new action `getSentimentXXX` - where XXX = your initials (so it has a unique name) - then ensure you select a `Runtime` of **Node.js 8**, and hit `Create`.

![](./images/11-create-get-sentiment.jpg)

You'll then be transported to a code editor. Delete all of the default lines of code within the editor, and replace them with these:

```Javascript
/**
  *
  * main() will be run when you invoke this action
  *
  * @param Accepts a text string 'payload' used for the NLU query
  *
  * @return JSON object containing:
  *         score: sentiment score obtained from Watson NLU for payload
  *
  */
function main({payload: payload}) {

    var NaturalLanguageUnderstandingV1 = require('watson-developer-cloud/natural-language-understanding/v1.js');
    var nlu = new NaturalLanguageUnderstandingV1({
      iam_apikey: '<nlu_api_key>',
      version: '2018-04-05',
      url: '<nlu_url>'
    });

    var promise = new Promise(function(resolve,reject) {
        nlu.analyze(
          {
            text: payload,
            features: {
              sentiment: {}
            }
          },
          function(err, res) {
            if (err) {
              console.log('error:', err);
              reject(err);
            } else {
              var score = res.sentiment.document.score;
              console.log('sentiment score = ', score);
              resolve({score: score});
            }
          }
        );
    });

return promise;
}
```
**(6)** You only need to make two small changes to this code.

- Replace `<nlu_api_key>` with the value of the **API key** you saved earlier from your _**Watson NLU**_ credentials
- Replace `<nlu_url>` with the value of the **URL** you saved from the same credentials

_Make sure you remove the `<>` signs when you do the replacements!_

Now hit `Save`.

![](./images/14-get-sentiment-code-complete.jpg)

The code accepts text as input (_payload_), calls the _**Watson NLU**_ service (using the _features_ parameter to specify the need for _sentiment analysis_), and returns the sentiment value (_score_).

**(7)** You can test _**IBM Cloud Functions**_ from within the editor. Click `Change Input`, replace the data in the `Change Action Input` window with the data below, and hit `Apply`.
```Javascript
{"payload": "The iPhone XR looks really cool and has a lot of great apps."}
```
![](./images/15-test-function.jpg)

**(8)** If you now select `Invoke`, the testing dialog will pass this payload text to the NLU service, and return a sentiment score.

![](./images/16-invoke-function.jpg)

**(9)** We've now successfully created an _**IBM Cloud Function**_ that can accept text as input and calculate and return a _sentiment score_ for that text. The final thing we need to do here is to make this function callable from within _**Watson Assistant**_ (or in fact, any other application).

Select `Endpoints` from the sidebar, tick the `Enable as Web Action` box, then `Save`.

![](./images/17-enable-as-web-action.jpg)

Now let's go and use our function in a new _**Watson Assistant**_ _intent_ and _dialog_.

## Create `Submit Review` _intent_ and _dialog_
**(1)** Let's start with building an _intent_ called `#submitreview` with some examples of text that a user might say to enter this dialog, e.g.
  - `Can I submit a review`
  - `I'd like to review a phone`
  - `Want to hear what I think of my mobile?`
  - `Here's a review of my phone`
  - `Can I give a review of my iPhone`
  - `Here's what I think of Google phones`
  - `I can give you a review of Samsung`

![](./images/20-intent-submit-review.jpg)

**(2)** Now let's create a `Submit Review` _dialog_ node below our `New contract` node, which tests for our `#submitreview` _intent_.

This time - instead of using _slots_ - after we've asked the user which brand they'd like to provide a review for, we'll present them with a list of available brand _**options**_ using the rich response capability within _**Watson Assistant**_.

Customise this node to **Then Respond with** `Option`. Use `Which phone manufacturer is your review for?` in the **Title** field, and add **List label** and **Value** pairs for `Apple`, `Google` and `Samsung`.

![](./images/20a-dialog-submit-review.jpg)

The **List label** field describes how the option will be presented to the chatbot user, whilst the **Value** field, contains the user input that will be passed to the _**Watson Assistant**_ service when this option is selected.

**(3)** If you test your new _intent_ and _dialog_ with `Try It` now, you'll see how _options_ are presented:

![](./images/21-options-try-it.jpg)

**(4)** Next, create a child node of `Submit Review`. This purpose of this node - `Save Brand` - is to save the brand just selected by the user in a _context variable_, and then ask the user for their review text.

Configure it as you see here, using the _Context Editor_ to save the `@brand` option selected in the variable `$brandForReview`, and responding to the user with `Please submit your $brandForReview review now, ensuring you enter more than 15 characters.`

![](./images/22-dialog-save-brand.jpg)

_Note:_ we are explicitly specifying a requirement for review text of at least 15 characters in length, as the _**Watson NLU**_ service will throw an error if fewer characters are provided as input. You could add a check for this when you've completed this lab if you want an additional challenge!

**(5)** The last thing the `Save Brand` node does is `Wait for user input`. This user input will be the review text that we want to send to our `getSentimentXXX` _**IBM Cloud Function**_.

Create a child node of `Save Brand`, called `Call getSentiment function`. Set **If assistant recognizes** to `true`, and open the node's _**JSON editor**_.

![](./images/23-call-getsentiment-function.jpg)

_**Watson Assistant's**_ _JSON editor_ provides an alternative method for defining responses. It's also the means by which we are able to call _**IBM Cloud Functions**_ from within a _dialog_.

**(6)** In the _JSON editor_, replace the existing code with this:
```Javascript
{
  "output": {
    "generic": []
  },
  "actions": [
    {
      "name": "<my-getSentiment-endpoint>",
      "type": "web_action",
      "parameters": {
        "payload": "<?input.text?>"
      },
      "result_variable": "$nluSentiment"
    }
  ]
}
```
The **only** thing you will need to replace here is `<my-getSentiment-endpoint>`. You can get the name of your _**endpoint**_ by going back to your _**IBM Cloud Function**_ in IBM Cloud, clicking `Endpoints` from the sidebar (if you're not already on that screen), then copying everything in the **Web Action URL** _after_ _**.../web/**_.

It should look something like:
```Javascript
jerry.seinfeld_dev/default/getSentimentXXX.json
```

![](./images/24-get-function-api-name.jpg)

![](./images/25-json-editor.jpg)

**(7)** When we reach this node in the _dialog_, we will now call our _**IBM Cloud Function,**_ passing `<?input.text?>` as our payload.

`<?input.text?>` is a special _**Watson Assistant**_ variable that always contains _the last input the user has provided_. In this case, this will be the review text we want to sent to our _**Watson NLU**_ sentiment analysis function.

You can see that we are also passing the _**IBM Cloud Function**_ credentials we set up earlier, and the data returned by the function will be stored in a _context variable_ we have called `$nluSentiment`.

**(8)** When the dialog executes our `getSentimentXXX` function, it will return a _sentiment score_ we can use to create a tailored response for the user. In fact, we are going to create two responses - one that informs the user whether the overall review is _positive, ambivalent or negative_, and one that is customised depending on the overall _strength_ of sentiment.

Create a child node of `Call getSentiment function`, and call it `Send Positive/Negative Message`. Use **If assistant recognizes** `true`.

Use `Customize` to ensure we enable **Multiple responses**, then configure **Then respond with** as:

If assistant recognizes  | Respond with
-------------------------|------------------------------------------------------------------------------------------------------------------------------
`$nluSentiment.score > 0 ` | `Watson calculates your $brandForReview review score to be <? T(Math).round($nluSentiment.score*100) ?>% positive.`
`$nluSentiment.score == 0` | `Watson calculates your $brandForReview review score to be 0% positive. Which to be fair, also means 0% negative.`
`$nluSentiment.score < 0`  | `Watson calculates your $brandForReview review score to be <? T(Math).abs(T(Math).round($nluSentiment.score*100)) ?>% negative.`

![](./images/26-send-positive-negative.jpg)

The variable `$nluSentiment` is a JSON object that contains the data we passed back from our _**IBM Cloud Function**_, which in this case contains a single variable named `score`, hence our comparator here is `$nluSentiment.score`.

The sentiment score is returned as a value between -1 and 1, to a number of decimal places. The
```Javascript
T(Math).abs(T(Math).round($nluSentiment.score*100))
```
code within the response performs the conversion to a more readable percentage format.

**(9)** Add a node below (at the same level as) `Send Positive/Negative Message` and call it `Send Feedback Message`. Again, use **If assistant recognizes** `true`, `Customize` to enable **Multiple responses**, then configure **Then respond with** as:

If assistant recognizes     | Respond with
----------------------------|--------------------------------------------------------------------------------------------------------
`$nluSentiment.score > 0.7`   | `It would seem that you are a really big fan of $brandForReview! Time for an upgrade!`
`$nluSentiment.score > 0.2 `  | `I guess you think $brandForReview phones are mostly OK. Try another one, maybe?`
`$nluSentiment.score > -0.2`  | `You sound pretty ambivalent about $brandForReview products. Might be time to check out other options.`
`$nluSentiment.score > -0.7`  | `OK so it looks like you don't really like $brandForReview phones. I'd go and check out the competition.`
`$nluSentiment.score <= -0.7` | `Woah! You had a pretty bad time with $brandForReview then? Definitely time for a change!`

![](./images/27-send-feedback-message.jpg)

**(10)** The final thing we need to do to complete our `Submit Review` dialog branch is ensure all of our **And finally** parameters are correct, and that we **reset our context variables** when we finish.

Working backwards, in our `Send Feedback Message` node, ensure we jump to `Help & Reset Context`.

![](./images/28-send-feedback-jump.jpg)

Then, configure the `Send Positive/Negative Message` node so it jumps to `Send Feedback Message`. This is to ensure that after we send the first message to the user, we immediately jump to the node that sends the second one.

![](./images/29-send-positive-negative-jump.jpg)

Next, change the `Call getSentiment function` node so that it will `Skip user input`, and immediately evaluate its two child nodes.

![](./images/30-call-get-sentiment-jump.jpg)

Finally, in our `Help & Reset Context` node, set the context variables `$brandForReview` and `$nluSentiment` to `null` so they are reset every time we complete a successful user interaction.

![](./images/31-reset-context.jpg)

**(11)** Test your new dialog with `Try It`. When it works, try it out with one of your _integrations_. It should look something like this:

![](./images/32-nlu-integration1.jpg)
![](./images/33-nlu-integration2.jpg)

## Summary
You've reached the end of this lab! By completing it you've learned how to further enhance your chatbot by calling additional services using _**IBM Cloud Functions**_. And you should now also understand what _**Watson Natural Language Understanding**_ is, and how to use _sentiment_ and _emotion_ with chatbots.

If you want to download the complete _**Watson Assistant**_ _skill_ we've built - up to and including this lab - you can do so [here](./assistant/skill-Phone-Advisor-lab-1D.json). If you do import this `skill`, you'll have to modify the `Call getSentiment function` node to refer to your `getSentimentXXX` _**IBM Cloud Function**_ details.

Now go to [Lab 5: Extending Your Chatbot with Watson Discovery](../5-Discovery) to discover how to increase your chatbot's knowledge base, by building _long-tail_ responses into your application!
