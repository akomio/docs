# Akomio Documentation

Platform documentation for Akomio Bots Engine

---

## What is Akomio

**Akomio** is a bot building platform oriented to developers. It allows to create your bots conversation flows using declarative json format.

## Basics

In Akomio bot is represented by a json configuration that describes all aspects of bot behavior.

Below is a sample echo bot configuration created with Akomio

```json
{
  "flows": [{
 	"id": "main",
    "steps": [{
  		"id": "echo",
      	"displayName": "Echo Message",
      	"type": "text",
       	"text": "You wrote: {{$message.text}}"
	}]
  }]
}
```



### Flows & Steps

Akomio uses concepts of flows and step to describe your bot behavior:

**Flow**  describes a bot conversation that is build around specific topic. For example collect user personal details, make a booking or perform a search. You can create as many flows for your bot. The first defined flow is considered to be an entry point flow and is executed when user starts new conversation with your bot. Flow consists of one or more steps.

**Step** is a building block of your bot. It describes specific user interaction with your bot like display text, buttons, ask for a number, date etc. There are multiple possible types of steps that are described below.

## Steps

Akomio flow step definition varies depending on step type, but all steps have the following properties defined:

- *id* - step unique id. Must be unique across the flow step is defined in.
- *displayName* - friendly step name to be displayed in Akomio system
- *type* - step type. Can have one of the following values:
  - *text* - bot will just display specified text to a user.
  - *actions* - bot will display action buttons to users.
  - *cards* - display image cards (with actions) to users.
  - *input* - ask user to enter some information like name, birthday, number of guests.
  - *remote* - akomio will call the specified URL to get step configuration and then execute returned configuration. Can be used for specific business logic that cannot be covered by other step types
- *condition* - boolean expression that defines whether step can be executed or not. 

### Context & Parameters

During flow execution akomio tracks conversation and put any user input in current user conversation context. You can access these parameters at any point using *{{PARAM_NAME}}* syntax. For example, if you want to greet user and make the message a bit more friendly, you can insert user's name:

```json
{
  "id": "hello",
  "displayName": "Say Hello",
  "type": "text",
  "text": "Hi {{$firstName}}, how can I help?"
}
```



Akomio presets the following parameters for any conversation:

- *$firstName* - user's first name based on his/her profile.
- *$lastName* - user's last name based on his/her profile.
- *$message.text* - current user's message

### Conditions

For every step you can specify a condition when the step can be executed. If the condition is not defined then step is always executed.

*Condition* is a boolean expression based on current parameters. For example, if you already know user's email you can skip the step asking for email:

```json
{
  "id": "inputEmail",
  "displayName": "Ask for email",
  "type": "input",
  "input_type": "email",
  "parameter": "email",
  "text": "What is your email address?",
  "condition": "email == ''"  
}
```



## Step Types

This section describes all possible step types in details.

### Text Step

Text step is the simplest step possible. All it does is sends a message to user with text specified. You can specify text via *text* step property.

*Hello text step:*

```json
{
  "id": "hello",
  "displayName": "Say Hello",
  "type": "text",
  "text": "Hi {{$firstName}}, how can I help?"
}
```



### Actions Step

Use actions step to present action buttons to users.

Actions are defined using *actions* step property and each action has the following definition:

- *title* - action's title
- *action* - action to execute when user press the button. See below for possible actions documentation.
- *options* - action options

#### Action Types

You can define the following action descriptions for your buttons:

- *next* - executes next step.

  ```json
  {
    "id": "hello",
    "displayName": "Say Hello",
    "type": "actions",
    "text": "Hi {{$firstName}}, how can I help?",
    "actions": [
      { "title": "Book Appointment", "action": "next" }
    ]
  }
  ```

  ​

- *step* - executes specific step in a flow. Step and flow are specified via *options* *parameter*

  ```json
  {
    "flows": [{
    	"id": "main",
      "steps": [{
        "id": "hello",
        "displayName": "Say Hello",
        "type": "actions",
        "text": "Hi {{$firstName}}, how can I help?",
        "actions": [{ 
            "title": "Book Appointment",
            "action": "step",
            "options": {"step": "book", "flow": "main"}
        }]
      },
      ...
      {
    	  "id": "book"
        ...
  	}]
    }]
  }

  ```

  ​

- *flow* - runs specific flow. After flow finishes to execute the current flow continues.

  ```json
  {
    "flows": [{
    	"id": "main",
      "steps": [{
        "id": "hello",
        "displayName": "Say Hello",
        "type": "actions",
        "text": "Hi {{$firstName}}, how can I help?",
        "actions": [{ 
            "title": "Book Appointment",
            "action": "flow",
            "options": {"flow": "clientDetails"}
        }]
      }]
    }, {
      "id": "clientDetails",
      "steps": [{
        "id": "phone",
        "type": "input"
        ...
  	}] 
    }]
  }
  ```

  ​

### Cards Step

Displays image content cards to users. Requies *cards* step property to define card. Card definition includes the following properties:

- *image_url* - web url to the image to be displayed for the card
- *title* - card title
- *subtitle* - card subtitle
- *actions* - card actions. (See Actions section). Max 3 actions per card is allowed. 

```json
{
  "id": "hello",
  "displayName": "Say Hello",
  "type": "cards",
  "text": "Hi {{$firstName}}, how can I help?",
  "cards": [
    {
      "image_url": "https://images.awesomedomain.com/111.jpg",
      "title": "Awesome Card",
      "subtitle": "This is an awesome card",
      "actions": [
  		{ "title": "Click Me", "action": "next"}
	  ]
    }
  ]
}
```

### Input Step

Input step allows you to get data from users. After user enters valid value it will be written as a value for a parameter specified in *parameter* step property. Input step required the following properties:

- *parameter* - parameter name the input will write the value to
- input_type* - type of the input. Can be on of the following:
  - *text* - text input. Any text user writes will be written as a parameter value.
  - *email* - email input. Entered value must be a valid email address
  - *date* - entered value must be a valid date. There is no restrictions on a date format as Akomio understands values like "next Friday", "July 13", "monday" etc.  
  - *time* - entered value must be a valid time
  - *number* - entered value must be a valid number

```json
{
  "id": "hello",
  "displayName": "Say Hello",
  "type": "input",
  "input_type": "email",
  "parameter": "email",
  "text": "What is your email address?"
}
```

### Remote Step

Remote step makes a POST request to an url specified in *url*  step property, passing all current parameters in a request body, and expects step configuration to be returned as a response. The returned configuration is executed.

```json
{
  "id": "showResults",
  "displayName": "Present Search Results",
  "type": "remote",
  "url": "https://api.myawesomesystem.com/search"
}
```

