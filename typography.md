

## Typography

[TOC]

### Introduction

Tamagui includes a default font family called [Inter](https://fonts.google.com/specimen/Inter) (`yarn add @tamagui/font-inter`) that can be used out of the box.  This guide is about installing and configuring a custom font family.

### Setup

1. Include the desired fonts (`.oft` or `.ttf`) in your project:
   - React Native apps must include the fonts as part of the build
   - Web apps may choose to use a CDN, or host as a static resource
2. Ensure the fonts are loaded properly for your target, for example:
   - [Expo](https://docs.expo.dev/versions/latest/sdk/font/)
   - [React Native](https://github.com/unimonkiez/react-native-asset)
   - Web - verify your CSS correctly defines the font family

### Getting Started

[Merriweather](https://fonts.google.com/specimen/Merriweather) is a free font from Google.  It was selected for this guide because it's a serif font (extra strokes on the ends of their letterforms), and it will be easy to visually verify your configuration is working.  Both `.oft` and `.ttf` file formats are supported, with `.otf` being a newer format, typically with a smaller size.  Both work on native and web targets.

Assume you have added and configured 3 fonts: 

1. MerriweatherBold
2. MerriweatherRegular
3. MerriweatherItalic

Let's add support to tamagui for regular, bold and italic fonts, and then build variants for each.

### Create the Font

```tsx
import { createGenericFont } from "@tamagui/config";

const merriweatherFont = createGenericFont(
	"Merriweather", // This should match the CSS rules to specify font family
	{
		size: {
			// Define default sizes 1-16 (all are required)
			// These become size token names, like `$1`, `$2`, etc.
			1: 11,
			2: 12,
			3: 13,
			4: 14,
			5: 16,
			6: 18,
			7: 20,
			8: 22,
			9: 30,
			10: 42,
			11: 52,
			12: 62,
			13: 72,
			14: 92,
			15: 114,
			16: 124,
		},
		transform: {
			1: "uppercase", // <Paragraph> will add `transform="uppercase"` for sizes 1-6. unspecified sizes will use the value from the previously before defined size
			7: "none", // <Paragraph> will add `transform="none"` for sizes 7-16
		},
		weight: {
			1: "600", // <Paragraph> will add `fontWeight="100"` for sizes 1-6
			7: "100", // <Paragraph> will add `fontWeight="600"` for sizes 7-16
		},
		color: {
			// Assigned colors based on sizes.
			1: "red", // <Paragraph> will add `color="red"` for size 1
			2: "$color", // <Paragraph> will add `color="$color"` for size 2+
		},
		letterSpacing: {
			1: 0.5, // Create a base
		},
		face: {
			"100": {
				// Remember we weight of 100 to size 7+
				italic: "MerriweatherItalic",
				normal: "Merriweather",
			},
			"600": {
				// And weight of 600 to size 1-6
				italic: "MerriweatherItalic",
				normal: "MerriweatherBold",
			},
			// You could include more for other weights, like thin, medium, etc.
		},
	},
	{
		sizeLineHeight: (val) => val * 1.2,
	},
);
```



## Use the font in your config

In your `tamagui.config.ts` file add the header and body

```tsx
const config = createTamagui({
  // Font Families
  fonts: {
    // header and body are required if you use <Heading> (<H1>...<H6>)  or <Paragraph> components
    heading: merriweatherFont,
    body: merriweatherFont,
    // mono: monoFont, // You can add any custom named font family you like, use fontFamily="$mono" to use this font family
  },
});
```



### Paragraph

Required font family: $body

```tsx
fonts: {
	body: bodyFont,
}
```



The <Paragraph> component is generally used for body content.  Its most important aspect is that is used the $color token to control its color.  This allows light and dark theming to be handled for you automatically. Using `<SizeableText>` or `<Text>` 

`<Paragraph>` has a color token of `$color`



### Headings

Required font family: $header

```tsx
fonts: {
	header: headerFont,
}
```



Heading inherit from `<Paragraph>` but token values, it's important to know these values so you can control the look of the design system. Although it's permitted, it is not recommended to specify a size on a header, for example `<H1 size="$2">Tamagui</H1>`. 

| Component | Size Token |
| --------- | ---------- |
| Heading   | `$8`       |
| H1        | `$10`      |
| H2        | `$9`       |
| H3        | `$8`       |
| H4        | `$7`       |
| H5        | `$6`       |
| H6        | `$5`       |



### Bolding & Italic

Bolding for `<Paragraph>` based components is controlled by the `fontWeight`, while `fontStyle` is used to control if the font is `italic` or `normal`.  This mimics how CSS and React Native work.



### Create Custom Typography Components

Adding **bold**, *italic* and <u>underlined</u> variants to `<Paragraph>`

```tsx
import { GetProps, styled } from "@tamagui/web";
import { Paragraph as TParagraph } from "tamagui";

const Paragraph = styled(TParagraph, {
	variants: {
		underline: {
			true: {
				textDecorationLine: "underline",
			},
		},
		bold: {
			true: {
				fontWeight: "600",
			},
		},
		italic: {
			true: {
				fontStyle: "italic",
			},
		},
	},
});

export default Paragraph; // Import this Paragraph as a drop-in replacement

```

