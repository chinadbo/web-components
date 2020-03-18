# web-components
web components including custom elements, shadow DOM and HTML templates.

## demo

### HTML

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Web Components</title>
  </head>
  <body>
    <user-card-one name="this is card one"></user-card-one>
    <user-card-two name="this is card two"></user-card-two>
    <user-card-two
      name="this is card two,too"
      avatar="https://randomuser.me/api/portraits/women/66.jpg"
    ></user-card-two>

    <user-card-two
      name="this is card two, too, too"
      avatar="https://randomuser.me/api/portraits/men/66.jpg"
    >
      <p slot="email">usercardtwo@email.com</p>
      <p slot="mobile">08079-8090-898</p>
    </user-card-two>

    <script src="userCard.js"></script>
  </body>
</html>

```

### JavaScript

```js
class UserCardOne extends HTMLElement {
  constructor() {
    super();
    this.innerHTML = `some text && ${this.getAttribute("name")}`;
  }
}

window.customElements.define("user-card-one", UserCardOne);

const template = document.createElement("template");
template.innerHTML = `
  <style> .user-card {
		font-family: 'Arial', sans-serif;
		background: #f4f4f4;
		width: 500px;
		display: grid;
		grid-template-columns: 1fr 2fr;
		grid-gap: 10px;
		margin-bottom: 15px;
		border-bottom: darkorchid 5px solid;
	}

	.user-card img {
		width: 100%;
	}

	.user-card button {
		cursor: pointer;
		background: darkorchid;
		color: #fff;
		border: 0;
		border-radius: 5px;
		padding: 5px 10px;
	}</style>
  <div class='user-card'>
    <img />
    <div>
    <h3></h3>
    <div class='info'>
      <p><slot name='email' /></p>
      <p><slot name='mobile' /></p>
    </div>
    <button id='toggle-info'>Toggle Info</button>
    </div>
  </div>
`;
class UserCardTwo extends HTMLElement {
  constructor() {
    super();
    this.showInfo = true;
    this.attachShadow({ mode: "open" });
    this.shadowRoot.appendChild(template.content.cloneNode(true));
    this.shadowRoot.querySelector("h3").innerText = this.getAttribute("name");
    this.shadowRoot.querySelector("img").src = this.getAttribute("avatar");
  }

  toggleInfo() {
    this.showInfo = !this.showInfo;
    const info = this.shadowRoot.querySelector(".info");
    const btn = this.shadowRoot.querySelector("#toggle-info");

    if (this.showInfo) {
      info.style.display = "block";
      btn.innerText = "Hide Info";
    } else {
      info.style.display = "none";
      btn.innerText = "Show Info";
    }
  }
  connectedCallback() {
    this.shadowRoot
      .querySelector("#toggle-info")
      .addEventListener("click", () => this.toggleInfo());
  }
  disconnectedCallback() {
    this.shadowRoot.querySelector("#toggle-info").removeEventListener("click");
  }
}

window.customElements.define("user-card-two", UserCardTwo);
```