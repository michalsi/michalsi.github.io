---
layout: post
title:  "(Really) Simple form in React"
date:   2021-01-03 19:37:59 +0100
categories: coding
tags: java-script react
---


![Z??wno??]({{ site.url }}/assets/2021-01-03-signup-form.png "Simpley Signup form")

Here's the code that includes:
 - Printing down signed up people
 - Cleareing out form
 - Basic validation in the input elements.
 - Additional _business_ validation

_css needs a bit of love._

```
import React from "react";
import isEmail from "validator/lib/isEmail";

const content = document.createElement("div");
document.body.appendChild(content);

module.exports = class extends (
  React.Component
) {
  constructor(props) {
    super(props);
    this.state = {
      fields: {
        name: "",
        email: "",
      },
      people: [],
      fieldErrors: {
        name: [],
        email: [],
      },
    };
  }

  static displayName = "my Basic";

  validateForm = (person) => {
    const errors = {};
    if (person.name === "bob") {
      errors.name = "name can not be bob obviously!";
    }
    if (person.email.length <= 6) {
      errors.email = "To short email!";
    }
    if (person.email && !isEmail(person.email)) {
      errors.email = "Ivalid email!";
    }
    return errors;
  };

  onFormSubmit = (e) => {
    const people = [...this.state.people];
    const person = this.state.fields;
    const fieldErrors = this.validateForm(person);
    this.setState({ fieldErrors });
    e.preventDefault();

    if (Object.keys(fieldErrors).length) return;

    this.setState({
      people: people.concat(person),
      fields: {
        name: "",
        email: "",
      },
    });
  };

  onInputChange = (e) => {
    const fields = Object.assign({}, this.state.fields);
    fields[e.target.name] = e.target.value;
    this.setState({ fields });
  };

  onClearForm = () => {
    const people = [...this.state.people];
    this.setState({
      people: people,
      fields: {
        name: "",
        email: "",
      },
    });
  };

  renderNamesList = () => {
    return (
      <div>
        <h2>People:</h2>
        {this.state.people.map(({ name, email }, i) => (
          <li key={i}>
            {name} - {email}
          </li>
        ))}
      </div>
    );
  };

  render() {
    return (
      <div>
        <h1>Sign up Form</h1>
        <form onSubmit={this.onFormSubmit}>
          <div>
            <label>Name</label>
          </div>
          <div>
            <input
              // className={
              //   this.state.fieldErrors.name ? "error-border" : "standardInput"
              // }
              placeholder="name"
              name="name"
              required
              value={this.state.fields.name}
              minLength="2"
              maxLength="10"
              onChange={this.onInputChange}
            />
            <span style={{ color: "red" }}>{this.state.fieldErrors.name}</span>
          </div>
          <div>
            <label>Email</label>
          </div>

          <div>
            <input
              className={
                this.state.fieldErrors.email ? "error-border" : "standardInput"
              }
              placeholder="email"
              name="email"
              required
              value={this.state.fields.email}
              onChange={this.onInputChange}
            />
            <span style={{ color: "red" }}>{this.state.fieldErrors.email}</span>
          </div>

          <input type="submit" />
          <input onClick={this.onClearForm} type="reset" value="Reset form" />
        </form>
        <div>
          <ul>{this.renderNamesList()}</ul>
        </div>
      </div>
    );
  }
};
```