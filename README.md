# Validating JSON offer & consuming outside data

Banditypes is a tiny schema validation library for TypeScript and JavaScript, designed to be extremely small, with a core size of around 400 bytes. Its primary focus is to provide a lightweight, efficient tool for validating and converting data structures while minimizing bundle size. For more in depth information regarding banidtypes click [here](https://zod.dev/).

> Question: Why are we using a schema validation?
> 
> Answer: When creating a JSON offer in Adobe Target, there are limited built-in safety measures and error-checking mechanisms. This lack of robust validation often leads to issues such as typos, missing properties, or incorrect values, which can lead to unexpected results being shown in our UI. To address these challenges, we have integrated schema validation into our codebase. This proactive approach ensures that any errors or unexpected issues are identified and resolved early, preventing faulty experiments from being executed. By validating the schema, we can confirm that all required properties  & values are present and correctly formatted before proceeding, thereby maintaining the integrity of our experimental offers.

When constructing a new experiment that consumes JSON data from Adobe Target, we need to implement schema validation to inform Banditypes of the expected data structure before applying any logic to the user interface. 

See the example below for reference.

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
  rotationIntervalMs: parse.numberOrOptional(); //Validates value is a number or property isn't listed in offer.
})

export default class ExampleExperiment implements Experiment<Offer> {
  async validate({ offer }: ExperimentValidationArgs): Promise<Offer> {
    //Passes offer to validate that we're receiving the data & types we expect from Adobe Target. 
    const parsed = parseOffer(offer)
    return parsed as Offer //Returns the offer otherwise throws an error.
  }
}
```


`src/validation/index.ts` - A wrapper for banditypes allowing us to customize the validation rules for our application. We will add to this object as our needs change.
```ts

```

400 bytes is an _approximate_ gzip bundle increase from using _all_ built-in validations. It may vary based on the minifier and the amount of validations used. A typical usage (primitives + object + array) is closer to 200 bytes, the core is around 100. Find out more about the [measurement technique.](#size-measurement)

If you like banditypes, check out [banditstash](https://github.com/thoughtspile/banditstash) — a tiny localStorage wrapper with runtime validation, fully configurable using plugins.
