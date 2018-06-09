# testflow
A command-line testing tool for Alexa skill code that simulates multi turn conversations, allows interactive inputs, and provides concise, colorful output.

Developers seek to stay in the "Flow", a mental state where they are able to design and code rapidly.
This tool is designed to streamline and simplify the development process for complex skill.

#### New Features
 * _Entity Resolution slots, Time dimension, simulating multiple unique users, testing Node.JS and Python projects_

See the [TUTORIAL](./tutorial/TUTORIAL.md)

<img src="https://m.media-amazon.com/images/G/01/cookbook/testflow_default._TTH_.png" alt="TestFlow" width="411" height="245">

### The Problem
Today many developers work on their skills in an interrupted fashion.
Imagine a developer deep in the coding flow and adding a nifty new feature.
In order to try the feature out, they
1. Zip their code project into a package
1. Upload the package to AWS Lambda
1. Launch the skill (on an Echo or by re-logging into the Developer portal)
1. Listen to the skill's welcome message
1. Ask a question or questions
1. Get an error
1. Return to the AWS browser tab and click CloudWatch
1. Open the latest log file and refresh it
1. Hunt for an console.log() message
1. Consider how this status message illuminates the code they were developing 60 seconds prior
1. Return to developing


With Testflow, the development process is simplified.  Once you add a new feature to your code, you can:
1. (Update a ```dialog.txt``` file with the series of Intents (& Slots) you wish to test)
1. Alt-Tab to your command prompt
1. Type ```node testflow``` and watch the conversation unfold
1. Review the speechOutput, sessionAttributes, card, errors and debug messages; in a concise colorful summary
1. Alt-Tab and return to developing


### Overview
Are you developing a conversational skill?  Maybe you are building a game, or a questionnaire, that requires several steps.
You may have seen how session attributes are set and recalled to allow the skill to remember things and give context to Yes and No answers.

<img align="right" src="https://s3.amazonaws.com/skill-images-789/tf/BreakfastTitle.gif">

A skill may prompt the user for inputs early in the conversation, store the responses in session attributes, and use the values to look up data or perform an action.
Game skills will keep track of user names, current scores, high scores, etc.
As a developer, in order to visualize the state of session attributes throughout a long skill session, it helps to be able to run a pre-defined sequence of events and observe everything that is happening at each stage.
Often it is difficult to visualize how your skill behaves as a "state machine" through many sequences of events.
It is taxing if you need to scour through log files or big JSON blocks like a detective, while simultaneously playing the role of the end user to execute the skill.
For example, imagine a quiz game with ten questions.  If you need to debug an issue with how the final score is calculated, you would have to manually invoke the quiz, step by step, until you reach the final state.

With Testflow, you can automate all ten answers, and have the option to pause the test sequence so you can type in a custom slot value, for example to test the quiz skill where the correct answer depends on a random question Alexa asks the user.


### Note:
Testflow is designed for human developers, and not for DevOps, CI/CD or as part of a build pipeline.
Testflow runs on your laptop; it requires no Internet connection (and is perfect for developing while on a plane).
It simply executes your code project (a local version of your Lambda code) with a sequence of events.

Testflow is not tied to ASK SDK V2.
It treats your code project like a black box, by simply sending in test events.
Your code can be running alexa-sdk V1, ask-sdk V2, or no SDK at all.


**```testflow.js```** is itself a Node.JS Javascript script designed to be run from the command line.  The script will access two other files:
 * Your ```src/index.js``` skill source code
 * A dialog sequence file, such as  ```dialogs/breakfast.txt```


#### Dialog Sequence File
Define a text file with your skill's input events.
Put one Request or Intent per line.  This corresponds to each of the Intent requests your code expects to receive from the Alexa service.

For example: *dialogs/default.txt*

```
LaunchRequest
AMAZON.HelpIntent
AMAZON.StopIntent
```

Another example: *dialogs/staterequest.txt*

```
LaunchRequest
StateRequestIntent usstate=Vermont
StateRequestIntent usstate=New%20York
ISeeIntent animal=bear color=brown
# AMAZON.HelpIntent
AMAZON.StopIntent
MyNameIsIntent myName=
MyNameIsIntent myName=Madeline
? StateRequestIntent usstate=Texas
RecapIntent
AMAZON.StopIntent
```

 * Notice that slot values with spaces need to be encoded.  Just insert ```%20``` to replace any white spaces, such as in ```usstate=New%20York```
 * You can prompt the user to confirm, or type in, a slot value by adding a leading ```? ``` to your Intent
 * You can comment out a line with a pound #

#### Running tests

1. Type ```node testflow```
  + You should see requests and responses for each of the default request types
1. Type ```node testflow breakfast.txt```
  + You should see request and Intents, slot values, session attributes, and output speech.


#### Customizing the output
At the top of the ```testflow.js``` file, notice a set of options you can define.
You may change any of these to ```true``` or ```false```.

```javascript
const options = {

    delay        : 1.0,    // delay N seconds between requests

    speechOutput : true,  // the cyan text you hear the Echo say

    reprompt     : false, // The reprompt in case the user does not answer

    attributes   : true,  // session.attributes shown in magenta
                          // You can also name one particular attribute to watch instead of the boolean

    slots        : true,  // key/value pairs shown in blue and green
                          // For Entity Resolution enter a pair such as red/red or crimson/red

    requestEvent : false, // the request JSON sent to your code

    cards        : false, // Display simple card title and text

    userId       : '123',  // Define the final 3 chars of the user Id, can be overridden

    timestamp    : ''      // defaults to Now, can set via '2018-04-23T21:47:49Z'

};
```
#### AWS Calls
If your code makes calls to AWS Services such as S3 or DynamoDB, you should be able to test these from your local command prompt, too.
Be sure you have the [AWS-SDK](./tutorial/SETUP.md) installed and the AWS CLI (command line interface) [installed](http://docs.aws.amazon.com/cli/latest/userguide/installing.html) and [configured](https://developer.amazon.com/blogs/post/Tx1UE9W1NQ0GYII/publishing-your-skill-code-to-lambda-via-the-command-line-interface).

#### Installation Steps
The setup instructions are found here: [SETUP](./tutorial/SETUP.md)

#### Tutorial
A tutorial is available at: [TUTORIAL](./tutorial/TUTORIAL.md) that shows off additional features.

#### Feedback
Feedback is appreciated!  Please create a Github Issue or Pull Request (above) to report problems or suggest enhancements.
Follow me at [@robmccauley](https://twitter.com/robmccauley)


