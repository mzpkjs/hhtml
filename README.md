# Hyper-HTML

## Getting Started

To serve Hyper-HTML from your server you need to compile your Hyper-HTML markup and render it to string.

```ts
import express from "express"
import hhtml from "hhtml"


const application = express();

// Compile Hyper-HTML markup to template trees
const file: string = require("./pages/index.html")
const template: string = hhtml.compile(file)

// An `/` endpoint serving basic html page
application.get('/', (request, response) => {
    const html: string = hhtml.serialize(template, { title: "Hello World!" })
    request.send(html)
})
```

Once the compiled Hyper-HTML is served to the client then you need to hydrate the document.

```ts
import hhtml from "hhtml"

document.onload = () => {
    hhtml.hydrate(document)
}
```

If you're not serving Hyper-HTML from the server then you can compile it just fine on the client.

```ts
import hhtml from "hhtml"

document.onload = () => {
    const template: string = hhtml.compile(document)
    hhtml.render(template)
}
```

### Directives

##### Value Interpolation & Binding

Use either `<tag>{{ <value> }}</tag>` or `<template data-x-bind>{{ <value> }}</template>`  
Lorem ipsum.

##### If Condition

Use either `<tag if(<boolean>)/>` or `<template data-x-if="<boolean>"/>`  
Lorem ipsum.

##### For Loop

Use either `<tag for(<value> in <iterable>)/>` or `<template data-x-for="<value> in <iterable>"/>`  
Lorem ipsum.

##### Unique Key

Use either `<tag key(<unique_value>)/>` or `<template data-x-key="<unique_value>"/>`  
Lorem ipsum.

##### Event Listener

Use either `<tag @<event_name>(<function_name>)/>` or `<template data-x-onclick="<function_name>"/>`  
Lorem ipsum.

### Inner workings

You can write your markup using a Hyper-HTML syntax.  
Here below you can see an example of a snippet using Hyper-HTML syntax.

```html

<main @root>
  <article for(post in posts) key(post.id) class="post">
    <h2>{{ post.title }}</h2>
    <p>{{ post.content }}</p>
    <section class="comments-section">
      <form if(user.canComment) class="your-comment">
        <input type="text">
        <button type="submit">Send</button>
      </form>
      <div for(comment in post.comments) key(comment.id) class="comment">
        <p><strong>Comment:</strong> <span>{{ comment.content }}</span></p>
        <div>
          <p for((reply, index) in comment.replies) key(index) class="reply">
            Reply: <span>{{ reply }}</span>
          </p>
        </div>
      </div>
    </section>
  </article>
</main>
```

A Hyper-HTML compiler will turn that snippet into an intermediate template trees that will be injected into the DOM.  
While a template tree is a still readable markup, it's a lot more verbose.

```html

<main data-x-root>
  <template data-x-for="post in posts" data-x-key="post.id">
    <article class="post">
      <template data-x-bind>
        <h2>{{ post.title }}</h2>
      </template>
      <template data-x-bind>
        <p>{{ post.content }}</p>
      </template>
      <section class="comments-section">
        <template data-x-if="user.canComment">
          <form class="your-comment">
            <input type="text">
            <button type="submit">Send</button>
          </form>
        </template>
        <template data-for-x="comment in post.comments" data-x-key="comment.id">
          <div class="comment">
            <p><strong>Comment:</strong>
              <template data-x-bind><span>{{ comment.content }}</span></template>
            </p>
            <div>
              <template data-x-for="(reply, index) in comment.replies" data-x-key="index">
                <p class="reply">
                  Reply:
                  <template data-x-bind><span>{{ reply }}</span></template>
                </p>
              </template>
            </div>
          </div>
        </template>
      </section>
    </article>
  </template>
</main>
```

Then Hyper-HTML engine will use these template trees to create actual DOM nodes.  
Those will be intertwined with corresponding template tree, so any node can be recreated in case such need occurs.

```html

<main data-x-root>
  <template data-x-for="post in posts" data-x-key="post.id">
    <article class="post">
      <template data-x-bind>
        <h2>{{ post.title }}</h2>
      </template>
      <template data-x-bind>
        <p>{{ post.content }}</p>
      </template>
      <section class="comments-section">
        <template data-x-if="user.canComment">
          <form class="your-comment">
            <input type="text">
            <button type="submit">Send</button>
          </form>
        </template>
        <template data-for-x="comment in post.comments" data-x-key="comment.id">
          <div class="comment">
            <p><strong>Comment:</strong>
              <template data-x-bind><span>{{ comment.content }}</span></template>
            </p>
            <div>
              <template data-x-for="(reply, index) in comment.replies" data-x-key="index">
                <p class="reply">
                  Reply:
                  <template data-x-bind><span>{{ reply }}</span></template>
                </p>
              </template>
            </div>
          </div>
        </template>
      </section>
    </article>
  </template>
  <article class="post">
    <template data-x-bind>
      <h2>{{ post.title }}</h2>
    </template>
    <h2>Hello World</h2>
    <template data-x-bind>
      <p>{{ post.content }}</p>
    </template>
    <p>Lorem ipsum, sid dolor set amet.</p>
    <section class="comments-section">
      <template data-x-if="user.canComment">
        <form class="your-comment">
          <input type="text">
          <button type="submit">Send</button>
        </form>
      </template>
      <form class="your-comment">
        <input type="text">
        <button type="submit">Send</button>
      </form>
      <template data-for-x="comment in post.comments" data-x-key="comment.id">
        <div class="comment">
          <p><strong>Comment:</strong>
            <template data-x-bind><span>{{ comment.content }}</span></template>
          </p>
          <div>
            <template data-x-for="(reply, index) in comment.replies" data-x-key="index">
              <p class="reply">
                Reply:
                <template data-x-bind><span>{{ reply }}</span></template>
              </p>
            </template>
          </div>
        </div>
      </template>
      <div class="comment">
        <p><strong>Comment:</strong>
          <template data-x-bind><span>{{ comment.content }}</span></template>
          <span>I don't like it</span>
        </p>
        <div>
          <template data-x-for="(reply, index) in comment.replies" data-x-key="index">
            <p class="reply">
              Reply:
              <template data-x-bind><span>{{ reply }}</span></template>
            </p>
          </template>
          <p class="reply">
            Reply:
            <template data-x-bind><span>{{ reply }}</span></template>
            <span>I don't like you too</span>
          </p>
          <p class="reply">
            Reply:
            <template data-x-bind><span>{{ reply }}</span></template>
            <span>I didn't mean that!</span>
          </p>
        </div>
      </div>
      <div class="comment">
        <p><strong>Comment:</strong>
          <template data-x-bind><span>{{ comment.content }}</span></template>
          <span>I don't like it</span>
        </p>
        <div>
          <template data-x-for="(reply, index) in comment.replies" data-x-key="index">
            <p class="reply">
              Reply:
              <template data-x-bind><span>{{ reply }}</span></template>
            </p>
          </template>
          <p class="reply">
            Reply:
            <template data-x-bind><span>{{ reply }}</span></template>
            <span>I don't like you too</span>
          </p>
          <p class="reply">
            Reply:
            <template data-x-bind><span>{{ reply }}</span></template>
            <span>I didn't mean that!</span>
          </p>
        </div>
      </div>
    </section>
  </article>
</main>
```

### Binding & Events

You can use a combination of inline event listeners and dynamic values to create interactive websites.

```html

<main @root>
  <section>
    <h1>Todo App</h1>
    <input type="text" placeholder="Add a task" value="{{description}}"/>
    <button @click="add()">Add Task</button>
    <ul>
      <li for((task, index) in tasks) key(index)>
        <label>
          <input
            @click="complete(task)"
            type="checkbox"
            checked="{{task.completed}}"
          />
          <span
            &style="text-decoration: line-through=?{{task.completed}};"
            &class="task task-done=?{{task.done}}"
          >
            {{ task.contents }}
          </span>
        </label>
      </li>
    </ul>
  </section>
</main>
```

This would compile to template trees like shown in previous examples.

```html

<main data-x-root>
  <section>
    <h1>Todo App</h1>
    <input data-x-model="description" type="text" placeholder="Add a task"/>
    <button data-x-onclick="add()">Add Task</button>
    <ul>
      <template data-x-for="(task, index) in tasks" data-x-key="index">
        <li>
          <label>
            <template data-x-bind>
              <input
                data-x-onclick="complete(task)"
                type="checkbox"
                checked="{{task.completed}}"
              />
            </template>
            <template data-x-bind>
                <span
                  data-x-style="text-decoration: line-through=?{{task.completed}};"
                  data-x-class="task task-done=?{{task.done}}"
                >
                  {{ task.contents }}
                </span>
            </template>
          </label>
        </li>
      </template>
    </ul>
  </section>
</main>
```


