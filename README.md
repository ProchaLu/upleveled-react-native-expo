- Blank typescript
- guest-list-mobile
- yarn android

# Let's learn React Native/Expo!

## 💻 [bit.ly/ul-rn-expo](https://bit.ly/ul-rn-expo-2023)

<div style="display:flex;">
<img src="https://user-images.githubusercontent.com/656318/155584657-9c36c352-e0bb-41c0-9c8b-ba11c0cdd509.png" alt="Screenshot showing Kind Words app with empty list" width="150"/> 
<img src="https://user-images.githubusercontent.com/656318/155584776-18683ba8-ac28-490d-984e-da040cd5829b.png" alt="Screenshot showing Kind Words app with new Post form" width="150"/> 
<img src="https://user-images.githubusercontent.com/656318/155584871-77d2f3f3-a0fb-486d-91c9-d693140071ed.png" alt="Screenshot showing Kind Words app with populated list" width="150"/> 
</div>

## 💜 Hi, I'm Ramón!

I'm on Twitter [@hola_soy_milk](https://twitter.com/hola_soy_milk), if you wanna get in touch!

Developer Relations, developer educator, inclusivity advocate. Over a decade of Software Engineering experience.

Originally from 🇨🇱.

## 🧰 Requirements

You should [already have everything needed installed](https://github.com/upleveled/system-setup/).

For this tutorial, we will be using Android Studio with Expo, which should be automatically taken care of when going through these instructions.

If you wish to work on your mobile device, go ahead and install [Expo Go](https://expo.dev/expo-go).

## ✨ Start up the project

Let's create a new Expo project:

```
yarn create expo-app --template
```

We're making a blank TypeScript app, as shown below:

Once everything is done installing, let's open it up and bring the terminal in:

    cd guest-list-mobile
    code .

Before proceeding, let's set up the [UpLeveled ESLint Config](https://github.com/upleveled/eslint-config-upleveled)!

First, let's take a look inside `package.json`:

```json
  "scripts": {
    "start": "expo start",
    "android": "expo start --android",
    "ios": "expo start --ios",
    "web": "expo start --web",
    "eject": "expo eject"
  },
```

Let's start up the app with `yarn start`. Our terminal will look like this:

With this running, press `a` (for Android!) and Android Studio will automatically start up, install Expo Go, and show your app.

## ✌️ Our `app.json` file

Notable options:

- `name`: Name of our app
- `slug`: URL-friendly code
- `version`: Version shown in the stores
- `orientation`: Which screen orientation is supported
- `icon`: App icon image
- `splash`: Settings for the screen shown while the app is loading
- `updates`: Configuration for hot-reloading
- `assetBundlePatterns`: Which files and assets to include
- `ios`: iOS-specific configuration
- `android`: Android-specific configuration
- `web`: Web-specific configuration

Let's set the background color of the splash screen and Android adaptive icon to be `"#f0e5cf"`.

## 🚶 First steps: the `styles` CSS object inside `./App.tsx`

> 🤔 Notice the naming style of CSS props in React Native

- Change the background color of the main view to `"#f0e5cf"`
- Change the text color to `"#3f3e41"` by creating a separate `text` object in the styles
- Let's extract `./styles/constants.ts` that exports a `colors` object with:

```typescript
export const colors = {
  background: "#f0e5cf",
  text: "#3f3e41",
  cardShadow: "#bfa2db",
  cardBackground: "#f0d9ff",
};
```

Apply those colors!

## ✍️ The `<Text>` component

Let's change the contents to "Guest List"! Not too different from React, eh?

## 🗣️ Creating a `<Header>` component

Let's have a header at the top of our app!

New file: `./components/Header.tsx`:

```typescript
import React from "react";
import { SafeAreaView, StyleSheet, View, Text } from "react-native";
import Constants from "expo-constants";
import { colors } from "../styles/constants";

type Props = {
  label: string;
};

export default function Header(props: Props) {
  return (
    <SafeAreaView style={styles.safeArea}>
      <View style={styles.container}>
        <Text style={styles.label}>{props.label}</Text>
      </View>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  safeArea: {
    backgroundColor: colors.cardBackground,
    width: "100%",
  },
  container: {
    paddingTop: Constants.statusBarHeight + 10,
    paddingBottom: 20,
  },
  label: {
    color: colors.text,
    fontSize: 32,
    textAlign: "center",
  },
});
```

From the [docs](https://reactnative.dev/docs/safeareaview):

> The purpose of SafeAreaView is to render content within the safe area boundaries of a device. It is currently only applicable to iOS devices with iOS version 11 or later.

Let's integrate it into our App, replacing our `<Text>` element, and with the label prop `"Guest List"`.

> CHALLENGE: How do we bring it to the top? Hint: Styles in App.tsx

## ✨ Integrating custom fonts

We'll be integrating the Google [Pacifico Font](https://fonts.google.com/specimen/Pacifico).

Let's do this using expo's packages:

    ./node_modules/.bin/expo install @expo-google-fonts/pacifico expo-font

Let's now integrate them into our App.tsx:

```typescript
import { StatusBar } from "expo-status-bar";
import { StyleSheet, View } from "react-native";
import Header from "./constants/Header";
import { colors } from "./styles/constants";
import { useFonts, Pacifico_400Regular } from "@expo-google-fonts/pacifico";

export default function App() {
  let [fontsLoaded] = useFonts({
    Pacifico_400Regular,
  });

  if (!fontsLoaded) {
    return null;
  } else {
    return (
      <View style={styles.container}>
        <Header label="Guest List" />
        <StatusBar style="auto" />
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: colors.background,
  },
  text: {
    color: colors.text,
  },
});
```

Let's now set the header's text to be `"Pacifico_400Regular"` with the `fontFamily` style.

## 🚴 Let's list our guests

We'll be using [FlatList](https://reactnative.dev/docs/flatlist).

Keep in mind the [structure of a `Guest` type](https://github.com/upleveled/express-guest-list-api-memory-data-store/blob/main/index.ts#L7)!

In `App.tsx`:

```typescript
type Guest = {
  id: string;
  firstName: string;
  lastName: string;
  deadline?: string;
  attending: boolean;
};

const renderItem = (item: { item: Guest }) => <GuestItem guest={item.item} />;

export default function App() {
  let [fontsLoaded] = useFonts({
    Pacifico_400Regular,
  });

  if (!fontsLoaded) {
    return null;
  } else {
    return (
      <View style={styles.container}>
        <Header label="Guest List" />
        <StatusBar style="auto" />

        <FlatList
          style={styles.list}
          data={guests}
          renderItem={renderItem}
          keyExtractor={(item: Guest) => item.id}
        />
      </View>
    );
  }
}
```

To our styles object in the same file, we'll add:

```typescript
list: {
    marginTop: 30,
    paddingLeft: 30,
    paddingRight: 30,
    width: '100%'
  },
```

Some things are a bit broken, it seems!

## 🚵 Our individual `GuestItem` component

Then let's define and style our guest item component. In `./components/GuestItem.tsx`:

```typescript
import { Text, StyleSheet, View } from "react-native";
import { colors } from "../styles/constants";

type Guest = {
  id: string;
  firstName: string;
  lastName: string;
  deadline?: string;
  attending: boolean;
};

type Props = {
  guest: Guest;
};

export default function PostItem({ guest }: Props) {
  const { firstName, lastName, attending } = guest;
  return (
    <View style={styles.card}>
      <Text style={styles.center}>
        {firstName} {lastName}
      </Text>
      <Text style={styles.right}>{attending ? "Coming!" : "Not coming."}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  right: {
    textAlign: "right",
    fontSize: 10,
  },
  center: {
    textAlign: "center",
  },
  card: {
    backgroundColor: colors.cardBackground,
    paddingTop: 10,
    paddingLeft: 30,
    paddingRight: 30,
    paddingBottom: 10,
    display: "flex",
    flexDirection: "column",
    justifyContent: "space-between",
    borderRadius: 30,
    marginBottom: 30,
    borderColor: colors.cardShadow,
    borderWidth: 0.5,
    borderTopWidth: 0,
    borderLeftWidth: 0,
    borderBottomRightRadius: 30,
    borderBottomLeftRadius: 30,
    borderTopRightRadius: 30,
    textAlign: "left",
  },
});
```

Still got one more undefined variable issue. Let's get right on it!

## 📮 Create list of static guests

In `App.tsx`, after defining `renderItem`:

```javascript
const guests = [
  {
    id: 1,
    firstName: "Miralda",
    lastName: "Flores",
    attending: true,
  },
  {
    id: 2,
    firstName: "Ximena",
    lastName: "Alvarez",
    attending: false,
  },
];
```

## ⚛️ React Hooks for guests

Let's replace our `guests` const with `useState`:

```javascript
let [guests, setGuests] = useState([
  {
    id: 1,
    firstName: "Miralda",
    lastName: "Flores",
    attending: true,
  },
  {
    id: 2,
    firstName: "Ximena",
    lastName: "Alvarez",
    attending: false,
  },
]);
```

Next we'll need a screen to add guests. That's where screens come in.

## 📱 Screens

We're gonna use `expo-router` to create navigable screens.

First, let's shut down the app in the terminal.