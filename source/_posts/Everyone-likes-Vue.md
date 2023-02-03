---
title: Everyone likes Vue
author: Rogers Kelamen
date: 2022-11-19 18:31:48
updated: 2022-11-20 11:50:00
index_img: https://s2.loli.net/2022/11/20/zMayOgbsrKV8CpB.png
banner_img: https://s2.loli.net/2022/11/20/JGEXUB7Al86IQqu.png
tags:
- vue
category:
- 前端
---

# Everyone likes Vue

Some docs you may need:
- [vue official website](https://cn.vuejs.org/)
- [vuetify -- a material design UI framework for vue](https://vuetifyjs.com/zh-Hans/) *(Note: for now vuetify doesn't support vue 3, but it's coming soon)*
- [nativescript-vue -- build your mobile app with vue](https://nativescript-vue.org/)
- [bootstrap-vue -- vue with popular bootstrap](https://bootstrap-vue.org/) *(Note: the vue version is 2, which may be outdated)*
- [pinia -- the next generation of Vue data store management](https://pinia.vuejs.org/)

## Some Alias

- v-on: --> @

  This for event trigger

  *for how to use modifiers, please see [docs](https://cn.vuejs.org/guide/essentials/event-handling.html#event-modifiers)*

- v-bind: --> :

  This for data binding -- dynamically responsive to model

One picture explains everything:
![complete directive syntax](https://cn.vuejs.org/assets/directive.69c37117.png)

*usually these two can handle most situations, except...*

## v-if Vs. v-show

One sentence to conclude: `v-if` decides whether to render the node(if the node is loaded), `v-show` decides whether to display the node(if the style append 'display: none;')

## v-for -- the list rendering

*Note: you shouldn't use both `v-for` and `v-if` in one scenario, cause their priorities are obscure*

Nothing to specially mention here:
```vue
<li v-for="(item, index) in items" :key="item.id">
  {{ index }} - {{ item.info }}
</li>
```
and for object:
```vue
<li v-for="(value, key, index) in myObjects">
  {{ index }}. {{ key }}: {{ value }}
</li>
```
or only value:
```vue
<span v-for="n in 10">{{ n }}</span>
```
*you can use `of` as substitution for `in`; and you can also use [destructuring assignment](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) here.*

**Note: recommend to add `:key` whenever you use `v-for`**

## v-mode Vs. v-bind

When should you use v-mode? Remember, v-mode is born for handling form inputs. So when you encounter something like `<input />`, it better remind you of v-mode.

Differently, v-mode is used for **double-binding**, which means not only can you change the data value from javascript, the user also has ability to change data just by changing input text.

Conclusion: v-mode for input text that you can see from the browser.

*you may be interested in [modifiers](https://cn.vuejs.org/guide/essentials/forms.html#modifiers) it provides*

## You should use `computed`

There is one main reason for you to better use 'computed': you're bothered about how to organize your binding-data logic

Once you want to compute a expression with complex logic, you should think about using `computed`. Think about following codes:
```vue
<script>
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  }
}
</script>
<template>
<span>{{ author.books.length > 0 ? 'Yes' : 'No' }}</span>
</template>
```
why not just try out `computed`:
```vue
<script>
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  },
  computed: {
    publishedBooksMessage() {
      return this.author.books.length > 0 ? 'Yes' : 'No'
    }
  }
}
</script>
<template>
<span>{{ publishedBooksMessage }}</span>
</template>
```
easy piece, right?

There even has more good feature: 'computed' only refreshes when the computation result is different from before.

**Note: Do not try to use side effects in `computed`, instead, use `watch`**

## Watch the update

Although `computed` can automatically execute when expression value is updated, it's not recommended to use that feature to execute some other functions(side effects) automatically.

Better use `watch`:
```vue
<script>
export default {
  watch: {
    // whenever the value of question is changed, the function bellow will execute.
    question(newQuestion, oldQuestion) {
      if (newQuestion.includes('?')) {
        this.getAnswer()
      }
    }
  },
}
</script>
```
### deep watch

Sometimes we want watch a value in Objects, then we need 'deep watch':
```vue
<script>
export default {
  watch: {
    someObject: {
      handler(newValue, oldValue) {
        // 注意：在嵌套的变更中，
        // 只要没有替换对象本身，
        // 那么这里的 `newValue` 和 `oldValue` 相同
      },
      deep: true
    }
  }
}
</script>
```
## Life Tree of Vue

![life circle](https://cn.vuejs.org/assets/lifecycle.16e4c08e.png)

## Let's meet Vue Component

![Vue Component](https://cn.vuejs.org/assets/components.7fbb3771.png)

If you want to import a child component, then you must [register it](https://cn.vuejs.org/guide/components/registration.html) in `components`:
```vue
<script>
import ButtonCounter from './ButtonCounter.vue'

export default {
  components: {
    ButtonCounter
  }
}
</script>

<template>
  <h1>Here is a child component!</h1>
  <ButtonCounter />
</template>
```
### data flow direction

- prop deliver(props): Parent --> Child
  ```vue
  <!-- Child.vue -->
  <script>
  export default {
    props: ['title']
  }
  </script>

  <template>
    <h4>{{ title }}</h4>
  </template>
  ```
  ```vue
  <!-- Parent.vue -->
  <BlogPost :title="myTitle" />
  ```
  then parent component changes the value of myTitle, it will make child component update the value of title.

- event watch($emit) : Child --> Parent
  ```vue
  <!-- Child.vue -->
  <template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button @click="$emit('enlarge-text')">Enlarge text</button>
  </div>
  </template>
  <!-- or -->
  <script>
  export default {
    emits: ['enlarge-text']
  }
  </script>
  ```
  ```vue
  <!-- Parent.vue -->
  <BlogPost
    ...
    @enlarge-text="postFontSize += 0.1"
   />
  ```
  By using `emit`, child component make the click event deliver to parent component so that child component is accessible to parents' function.

## Make your own directive
```vue
<script>
const focus = {
  mounted: (el) => el.focus()
}

export default {
  directives: {
    // 在模板中启用 v-focus
    focus
  }
}
</script>
<template>
<input v-focus />
</template>
```
You can see `mounted` here, this hook is provided by Vue, you can find [more hooks here](https://cn.vuejs.org/guide/reusability/custom-directives.html#directive-hooks)

