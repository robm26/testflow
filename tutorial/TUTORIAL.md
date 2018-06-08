## Testflow Tutorial

Welcome!  This guide will walk you through all the features of Testflow using two included sample skill projects.

First, be sure Testflow is installed: see [SETUP.md](./SETUP.md)

We will be testing against the two included Node.JS sample skill code projects in
[sampleskill](../sampleskill) and
[sampleskill2](../sampleskill2) and the Python project in
[sampleskill3](../sampleskill3)

Open up the testflow project folder in your favorite code editor or text editor.
We will be modifying ```testflow.js```, the code at ```skillsample/index.js```, and the sequence text files within ```/dialogs/```.

Open up a commmand prompt (black background is recommended),
change into the root testflow directory, and type ```node testflow```

### Choose Test
1. Type ```node testflow breakfast.txt```
1. Type ```node testflow lunch.txt``` You will get an error.
1. Create a new file in /dialogs called lunch.txt with Requests & Intents similar to breakfast.txt
1. Run testflow again with your new file.

### Delay
1. Within testflow.js, change the options.delay to 4 seconds.
1. Rerun the test.  You now have time to read the responses from your Alexa skill.

### Customized Output

1. Set the following options to true:
  * options.speechOutput
  * options.reprompt
  * options.attributes
  * options.cards
1. Run ```node testflow breakfast.txt```

  * Pay close attention to the session attributes in magenta.  A design goal of Testflow is to allow you to easily view these memory values as the skill progresses.

### Basic Slots
1. Run ```node testflow attraction.txt```
You should see a slot key:value pair in blue and green.
1. Modify attraction.txt by inserting a question-mark ? at the beginning of the AttractionIntent line.
This will cause the script to pause and give you a chance to type in a value.
1. Run ```node testflow attraction.txt``` again.
You can type in "40" and the skill will execute with this new value.  You may see an attraction a little farther away!

  * The real Alexa service would attempt to fill this slot with a valid AMAZON.NUMBER value.  If you were to utter "unicorn", the Alexa service would only this slot with simply "?"


### Calling Web Services & APIs
1. Run ```node testflow outside.txt```
  * The skill attempts a live call to a Yahoo API to get the current weather.
  As long as your laptop has Internet access, you should be able to execute the test and see the results.

### Debugging
1. Run ```node testflow dinner.txt```
1. The error is caught and displayed in white text.
1. Open sampleskill/index.js and find the word "unicorn" and comment it out.



### Misc
1. Run ```node testflow coffee.txt```
1. Notice the final line is an AMAZON.YesIntent, but the skill doesn't understand.
  * The skill has ended with the first YES.  You can see double-dashed lines (=====) indicating the skill session has ended.
The next YES is running under a brand new session.
1. Open coffee.txt and comment out the final line with a pound sign: ```# AMAZON.YesIntent```
1. You could also insert a new line with just the word ```end``` in order to skip any remaining lines.


### Skill Sample 2 Setup
1. Right at the top of testflow.js, update source code reference to point to sample #2:
```const MyLambdaFunction = require('./sampleskill2/index.js');```

This skill is more complex; be sure you have setup the AWS CLI and AWS SDK as recommended in the [SETUP](./tutorial/SETUP.md) steps.
The skill uses a feature of the ask-sdk to auto-create a DynamoDB table for you, called ```askMemorySkillTable```.
This table is used to store persistent attributes so the skill can remember each user even after the session ends.
If you get errors, check your [AWS DynamoDB Console](https://console.aws.amazon.com/dynamodb/home) to see if this table is still being created.
You may need to visit the [IAM Console](https://console.aws.amazon.com/iam/home)
to grant your CLI user (and Lambda role) access to DynamoDB, for example by attaching the ```AmazonDynamoDBFullAccess``` policy.

### Attributes
1. Set the following options to false:
  * options.reprompt
  * options.cards
1. Verify the following options are true:
  * options.slots
  * options.attributes
1. Run ```node testflow color.txt```

   * Wow, there are a lot of attributes being tracked! One of the attributes is called ```favoriteColor```.
Let's focus in on that attribute only.  The options.attributes can be set to more than just a true/false boolean.
1. Set options.attributes to the string 'favoriteColor'.
1. Re-run ```node testflow color.txt```

### Entity Resolution Slots
Now try ```node testflow colorsynonym.txt```

In your skill's language model you can define a custom slot with just a list of your own values.
In this simple case, your code would receive a slot value (or null) as part of the Intent.
Even if the user were to say "unicorn", that value would be returned as the slot value (whether or not it exists in your custom slot values).

If you create a custom slot AND define synonyms,
then an advanced Alexa feature called [Entity Resolution](https://developer.amazon.com/blogs/alexa/post/5de2b24d-d932-4c6f-950d-d09d8ffdf4d4/entity-resolution-and-slot-validation) kicks in.

A user's slot utterance could now either:
 * Not be heard at all (Unfilled slot value)
 * Match to a slot value directly (called ER_SUCCESS_MATCH)
 * Match to a synonym of a slot value (also ER_SUCCESS_MATCH, but with both values returned)
 * Be heard, but fail to match to any value or synonym (ER_SUCCESS_NOMATCH)

We can simulate each of these cases in Testflow.
```
MyColorSetIntent color=
MyColorSetIntent color=red/red
MyColorSetIntent color=crimson/red
MyColorSetIntent color=unicorn/
```

### Time dimension
Now try ```node testflow time.txt```

The Alexa service (and AWS Lambda) operate on UTC time.  A current timestamp is included with each request to your code.
Sometimes we want to simulate a history of interactions prior to the current time.

Testflow allows you to run your requests in the past (or future) by specifying a time offset.
For example, to specify six hours in the past,
you can add a line to your sequence file with beginning with ```@``` and including a timespan to add to the current time.

Timespans can be defined as minutes, hours, or days.
To execute a request as of a week ago, you can add a line: ```@ -7d```

```
@ -7d
LaunchRequest
AMAZON.StopIntent
@ -6h
LaunchRequest
AMAZON.StopIntent
@ -30m
LaunchRequest
AMAZON.StopIntent
@ 0m
LaunchRequest
AMAZON.StopIntent
```

### Different Users
Now try ```node testflow colorusers.txt```

Your published skill will be used by multiple users, each with a unique userId.
Testflow can vary the userId to simulate different user sessions.  The ```userId``` field's
last three characters can be set via a directive in the dialog sequence file.

```
~ 111
LaunchRequest
AMAZON.StopIntent
~ 222
LaunchRequest
AMAZON.StopIntent
~ 333
LaunchRequest
AMAZON.StopIntent
```

### Python
Testflow supports testing Python code as well as Node.JS.
The tool checks the extension of your source file, either .js or .py.
For Python projects, a child process is spawned that executes the tests via Python.exe -c commands.
Within testflow.js, search for // PYTHON to see the this code.


Open up testflow.js and modify the location of your Python handler as shown here:

```
const SourceCodeFile = './sampleskill3/index.py';
const handlerName =  'lambda_handler';
```

Now try ```node testflow colorusers.txt```



### DynamoDB Local
If your skill uses Persistent attributes and DynamoDB,
you would need to have Internet access to AWS for Testflow to run your skill properly.

However, you could [setup a local instance of DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.html),
for enabling offline testing (like when traveling).

Then, modify your code to define a new local DynamoDB client and specify it in your skill handler:

```
const localDynamoClient = new AWS.DynamoDB({apiVersion : 'latest', endpoint : 'http://localhost:8000'});
...
exports.handler = skillBuilder
    .addRequestHandlers(
    ...
    )
.withTableName(DYNAMODB_TABLE)
.withDynamoDbClient(localDynamoClient)
```
