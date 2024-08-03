> # Holy Grail layout

```html
<style>
  html,body{
    height: 100%;
  }
  .wrapper{
    min-height: 100%;
    display: flex;
    flex-direction: column;
  }
  .middle{
    display: flex;
    flex: 1;
  }
  main{
    flex: 3;
  }
  nav, aside{
    flex: 1;
  }
  footer{
    margin-top: auto;
  }
</style>

<div class="wrapper">
  <header class="box">Header</header>
  <section class="middle">
    <nav class="box">Nav</nav>
    <main class="box">Main Content</main>
    <aside class="box">Ad unit</aside>
  </section>
  <footer class="box">Footer</footer>
</div>

```

> # Sticky Sidebar

```html
<style>
  .wrapper {
    display: flex;
  }

  nav {
    position: sticky;
    top: 0;
    align-self: flex-start;
    border: solid deeppink;
  }

  main {
    flex: 1;
  }
</style>

<section class="wrapper">
  <nav class="box">
    <h2>Navigation</h2>
    <ul>
      <li>Section One</li>
      <li>Section Two</li>
    </ul>
  </nav>
  <main class="box">
    <p>This container contains random stuff to increase its height.</p>
    <img src="https://courses.joshwcomeau.com/cfj-mats/cat-300px.jpg" />
    <p>Normally, a blog post would exist in this container.</p>
    <img src="https://courses.joshwcomeau.com/cfj-mats/dog-one-300px.jpg" />
    <p>This container contains random stuff to increase its height.</p>
    <img src="https://courses.joshwcomeau.com/cfj-mats/cat-two-300px.jpg" />
    <p>Normally, a blog post would exist in this container.</p>
    <img src="https://courses.joshwcomeau.com/cfj-mats/dog-two-300px.jpg" />
  </main>
</section>
```

> # Css Grid Snippet World Famous

```html
<style>
  .grid {
    display: grid;
    gap: 16px;
    grid-template-columns:
      repeat(auto-fill, minmax(150px, 1fr));
  }
</style>

<main class="grid">
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
</main>
```

```html
<style>
  .grid {
    --min-column-width: min(320px, 100%);
    display: grid;
    grid-template-columns:
      repeat(auto-fill, minmax(var(--min-column-width), 1fr));
    gap: 16px;
    padding: 16px;
  }
</style>

<main class="grid">
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
  <div class="item"></div>
</main>

```

> # auto-fill vs auto-fit
```css
.grid-container {
    display: grid;
}

.grid-container--fill {
    grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
}

.grid-container--fit {
    grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
}

.grid-element {
  background-color: deepPink;
  padding: 20px;
  color: #fff;
  border: 1px solid #fff;
}


body {
  padding: 2em;
}

hr {
  margin: 80px;
}
```

```html
auto-fill
<div class="grid-container grid-container--fill">
  <div class="grid-element">
    1
  </div>
  <div class="grid-element">
    2
  </div>
  <div class="grid-element">
    3
  </div>
  <div class="grid-element">
    4
  </div>
  <div class="grid-element">
    5
  </div>
  <div class="grid-element">
    6
  </div>
  <div class="grid-element">
    7
  </div>
</div>

<hr>

auto-fit
<div class="grid-container grid-container--fit">
  <div class="grid-element">
    1
  </div>
  <div class="grid-element">
    2
  </div>
  <div class="grid-element">
    3
  </div>
  <div class="grid-element">
    4
  </div>
  <div class="grid-element">
    5
  </div>
  <div class="grid-element">
    6
  </div>
  <div class="grid-element">
    7
  </div>
</div>
```
