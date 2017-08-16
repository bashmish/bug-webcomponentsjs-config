# bug-webcomponentsjs-config

If you enforce ShadyDOM by configuring polyfill using global vars before importing the polyfill itself like this

    <script>window.ShadyDOM = { force: true };</script>
    <script>window.customElements = { forcePolyfill: true };</script>
    <script src="bower_components/webcomponentsjs/webcomponents-lite.js"></script>

then document.createElement('...') for custom elements does not initialize element correctly.

Check this by running polymer server

    bower install;
    polymer serve -o;

and you'll get

    my-component.html:18 Uncaught TypeError: Cannot read property 'myLightDomDiv' of undefined
        at HTMLElement.checkLightDom (my-component.html:18)
        at HTMLElement.ready (my-broken-component.html:15)
        at HTMLElement.ready (class.html:217)
        at HTMLElement._enableProperties (property-accessors.html:531)
        at HTMLElement.connectedCallback (element-mixin.html:614)
        at HTMLElement.connectedCallback (legacy-element-mixin.html:90)
        at CustomElementRegistry.Fa.define (style-placeholder.js:36)
        at Function.window.Polymer._polymerFn (polymer-fn.html:43)
        at window.Polymer (boot.html:29)
        at my-broken-component.html:10

If you remove these two lines of configuration and use GET params `?wc-shadydom=true&wc-ce=true` instead, it works OK.

Try it (possible local server address http://127.0.0.1:8081/components/bug-webcomponentsjs-config/?wc-shadydom=true&wc-ce=true) and you'll see in the console

    my-component.html:14 Ready works!
    my-component.html:19 Component LightDOM is initialized!

This works even you leave these lines in the code, so looks like GET params override them anyway.

    <script>window.ShadyDOM = { force: true };</script>
    <script>window.customElements = { forcePolyfill: true };</script>

It breaks under Polymer 1.9.x too.

And also breaks without `window.customElements` line, having only `<script>window.ShadyDOM = { force: true };</script>`.
