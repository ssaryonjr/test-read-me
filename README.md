# Validating JSON offer & consuming outside data

Banditypes is a tiny schema validation library for TypeScript and JavaScript, designed to be extremely small, with a core size of around 400 bytes. Its primary focus is to provide a lightweight, efficient tool for validating and converting data structures while minimizing bundle size. For more in depth information regarding banidtypes click [here](https://zod.dev/).

> Small size is the primary focus of banditypes. This library will allow us to handle the way our code consumes outside data in a safer and more predictable manner. 

```ts
import parse from '../validation' // 'Parse' is the convention we will be using.

interface Offer {
  messages: Message[];
  rotationIntervals?: number
}

interface Message {
  text: string;
  href: string;
}

//Defining the banditype schema that will be called during our validation phase.
const parseOffer = parse.object({ //Validate that we're receiving an object.
  message: parse.array( //Checks that 'message' prop has a value of an array.
    parse.object({ //Checks that values within the array are objects.
      text: parse.nonEmptyString(), //Validate this is a NON empty string
      href: parse.string(), //Validates this value is a string
     })
  ),
  rotationIntervalMs: parse.numberOrOptional(); //Validates value is a number or property isn't listed.
})

export default class ExampleExperiment implements Experiment<Offer> {
  async validate({ offer }: ExperimentValidationArgs): Promise<Offer> {
    //Offer passed into parseOffer validates that we're receiving the data we expect from Adobe Target. 
    const parsed = parseOffer(offer)
    return parsed as Offer //Returns the offer otherwise throws an error.
  }
}

//Passes the JSON offer to parseOffer function to validate the data we expect is what we're receiving from Adobe Target.

```


`src/validation/index.ts` - A wrapper for banditypes allowing us to customize the validation rules for our application. We will add to this object as our needs change.
```ts

```

400 bytes is an _approximate_ gzip bundle increase from using _all_ built-in validations. It may vary based on the minifier and the amount of validations used. A typical usage (primitives + object + array) is closer to 200 bytes, the core is around 100. Find out more about the [measurement technique.](#size-measurement)

If you like banditypes, check out [banditstash](https://github.com/thoughtspile/banditstash) — a tiny localStorage wrapper with runtime validation, fully configurable using plugins.
