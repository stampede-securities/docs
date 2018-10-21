# Styling

## General

### Essentials

* [Styled Components: Basics](https://www.styled-components.com/docs/basics)
* [Basic webpages](https://internetingishard.com/html-and-css/basic-web-pages/)
* [Flexbox](https://internetingishard.com/html-and-css/flexbox/)
* [CSS Intro](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/CSS_basics)

####  Concepts

* Flexbox
* Padding/margin
* Color
* Fonts
* Positioning

### Recomended

* [Styled Components: Theming](https://www.styled-components.com/docs/advanced#theming)
* [Positioning](https://internetingishard.com/html-and-css/advanced-positioning/)
* [Flexbox reference](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

## React

### Recomended

* [Typography](https://internetingishard.com/html-and-css/web-typography/)
* [Media queries](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries)
* [Styled Components: Media Templates](https://www.styled-components.com/docs/advanced#media-templates)
* [Responsive Design](https://internetingishard.com/html-and-css/responsive-design/)
* [Responsive Images](https://internetingishard.com/html-and-css/responsive-images/)
* [Forms](https://internetingishard.com/html-and-css/forms/)

Component libraries. If you want to design custom components, use [rebass](https://github.com/jxnblk/rebass).

If you want a premade design system, choose a component library with the right aesthetic. This [list](https://blog.bitsrc.io/11-react-component-libraries-you-should-know-178eb1dd6aa4) or this [one](https://github.com/alexpate/awesome-design-systems) may be helpful. Material-ui maybe a good default.

### Examples

* https://github.com/outline/outline

## React Native

Use Shoutem for components with totally custom styles. Use NativeBase for components which shadow the native components.

### Recommended

* [Style](https://facebook.github.io/react-native/docs/style.html)
* [Height and Width](https://facebook.github.io/react-native/docs/height-and-width.html)
* [Layout with flexbox](https://facebook.github.io/react-native/docs/flexbox.html)
* [NativeBase](https://docs.nativebase.io/Components.html#Components)
* [Shoutem UI](https://shoutem.github.io/docs/ui-toolkit/introduction)
* [Material Design](https://material.io/design/environment/surfaces.html#material-environment)
* [Apple Human Interface Guidelines](https://developer.apple.com/ios/human-interface-guidelines/overview/themes/)
* [Stylelint-processor-styled-components](https://github.com/styled-components/stylelint-processor-styled-components)

#### Android vs iOS design patterns

* [Better Cross-Platform React Native Components](https://medium.com/differential/better-cross-platform-react-native-components-cb8aadeba472)
* [Interaction Design patterns: iOS vs Android](https://medium.com/@vedantha/interaction-design-patterns-ios-vs-android-111055f8a9b7)

### Examples

* https://github.com/gitpoint/git-point/

## Guidelines

Aim to separate the style from the layout. Styles should go in the styled components while the layout should be passed as a prop.

```
import { Styled } from react-native-row

const GreenRow = Styled.Row({
    backgroundColor: "green"
})

<GreenRow flex={2}>
    <Text>Hi</Text>
</GreenRow>
```

## Resources

* [Awesome Styled Components](https://github.com/styled-components/awesome-styled-components)
* [Styled Components Chat](https://spectrum.chat/styled-components)
