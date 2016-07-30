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

### Text Step

### Actions Step

### Cards Step

### Input Step

#### Text

#### Date

#### Time

#### Number

### Remote Step