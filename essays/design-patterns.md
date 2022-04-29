---
layout: essay
type: essay
title: Learning From The Past With Design Patterns
date: 2022-04-28
labels:
  - Design Patterns
  - Software Engineering
---

<img class="ui large image" src="../images/eslintlogo.png">

## Design Patterns and Its Cooler but Dangerous Enemy, Antipatterns

Patterns are repeatable and predictable elements that are used in many different fields of study. In science, patterns are used to predict future events and create models using that data. In art, patterns are designs of repeated shapes, lines, or colors. This wide definition of patterns also extends into the computer science field with design patterns. In software engineering, design patterns are repeatable solutions to common problems that occur in software design. They allow for effective development of software by providing templates that developers can simply fill in. It also lets developers in a team have common ground on which to build their programs on. Examples of design patterns include Abstract Factory, Bridge, and Interpreter. There is also an opposite to design patterns known as antipatterns. They are inefficient and ineffective practices that can lead to many problems down the road. Examples of antipatterns include God Object, Golden Hammer, and Poltergeist. Despite the cool names, these should be avoided so that your code is clean and efficient. 

## Crticism Of Design Patterns

Design patterns have many pros but I think there are important cons to discuss as well. For all of the advantages of using design patterns, there are also disadvantages to using them. A design pattern can be complicated to implement or learn, can lead to performance issues, or be difficult to look through. These disadvantages will be dealt with as you spend more time with the design pattern but it is important to remember these down sides so they can be patched up. Another issue with design patterns is it can be misused when there is a more efficient or more simple solution. Having a general outline for problems is a good way to get a simple solution. However, the design pattern may miss out on more efficient solutions that are more specific to a certain language or situation. Design patterns can also make a solution more complicated than it has to be, which causes unnecessary complexity. These cons to design patterns can be overcome by knowing when to use them and being able to adapt the design pattern to your specific situation. 

## Design Patterns in Action

For my final project for ICS 314, our team used a few design patterns in our code. One of the design patterns used is the Singleton design pattern. In 'Students.js' there is a constructor for a 'Student' object and an export statement for the singleton instance. Here, the variable 'Students' is the singleton instance of the 'StudentCollection' class. ‘Students’ is now used as a global variable, being accessed and manipulated wherever it needs to be.

Students.js
```
import { Mongo } from 'meteor/mongo';
import SimpleSchema from 'simpl-schema';
import { Tracker } from 'meteor/tracker';

/** Encapsulates state and variable values for this collection. */
class StudentsCollection {
  constructor() {
    // The name of this collection.
    this.name = 'StudentsCollection';
    // Define the Mongo collection.
    this.collection = new Mongo.Collection(this.name);
    // Define the structure of each document in the collection.
    this.schema = new SimpleSchema({
      email: { type: String, index: true, unique: true },
      firstName: { type: String, optional: true },
      lastName: { type: String, optional: true },
      description: { type: String, optional: true },
      picture: { type: String, optional: true },
      location: { type: String, optional: true },
    }, { tracker: Tracker });
    // Ensure collection documents obey schema.
    this.collection.attachSchema(this.schema);
    // Define names for publications and subscriptions
    this.userPublicationName = `${this.name}.publication.user`;
    this.adminPublicationName = `${this.name}.publication.admin`;
  }
}

export const Students = new StudentsCollection();
```

Another design pattern we used is Publish Subscribe. In 'Publications.js', there are 4 collections that are being published to the event bus that contain 4 different objects in those collections. A subscriber to the 'Companies' collection is 'Company.jsx'. This page subscribes to the 'Companies' collection to get the data in the collection so that it can display the companies information on cards.

Publications.js
```
import { Meteor } from 'meteor/meteor';
import { Students } from '../../api/students/Students';
import { Companies } from '../../api/companies/Companies';
import { StudentsInterests } from '../../api/students/StudentsInterest'
import { Interests } from '../../api/interests/Interests'

Meteor.publish(Students.userPublicationName, () => Students.collection.find());
Meteor.publish(Companies.userPublicationName, () => Companies.collection.find());
Meteor.publish(Interests.userPublicationName, () => Interests.collection.find());
Meteor.publish(StudentsInterests.userPublicationName, () => StudentsInterests.collection.find());

Meteor.publish(null, function () {
  if (this.userId) {
    return Meteor.roleAssignment.find({ 'user._id': this.userId });
  }
  return this.ready();
});
```

Companies.jsx
```
/** withTracker connects Meteor data to React components. https://guide.meteor.com/react.html#using-withTracker */
export default withTracker(() => {
  // Ensure that minimongo is populated with all collections prior to running render().
  const sub1 = Meteor.subscribe(Companies.userPublicationName);
  return {
    ready: sub1.ready(),
  };
})(CompaniesPage);
```
