# 10 AI
Velkommen til den sidste øvelse og i dag skal vi lave vores egen chatbot på OpenAI's API. Få din gruppes API key af en af os. Husk ikke at spamme den med spørgsmål, da vi kun har et begrænset antal credits. 

## Opsætning
Start med at oprette et nyt react-native projekt med expo-cli. 

``` npx create-expo-app GenAI_Chat --template blank```

Naviger til projektmappen med cd og kør projektet

Når projektet er startet op, kan du åbne det i en simulator eller på din telefon. 
Sluk herefter serveren og erstat jeres package.json dependencies med følgende:
```javascript
"dependencies": {
    "@react-native-async-storage/async-storage": "^2.2.0",
    "@react-navigation/stack": "^6.4.1",
    "expo": "~54.0.12",
    "expo-status-bar": "~3.0.8",
    "openai": "^4.65.0",
    "react": "19.1.0",
    "react-native": "0.81.4",
    "react-native-async-storage": "^0.0.1",
    "react-native-dotenv": "^3.4.11",
    "react-native-gifted-chat": "^2.6.3",
    "react-native-reanimated": "~4.1.1"
  },
  "private": true,
  "devDependencies": {
    "babel-preset-expo": "^54.0.3"
  }
```

Kør herefter `npm install` i jeres projekt, så I downloader de nødvendige pakker.

Som I kan se fra overstående dependencies, skal vi arbejde med OpenAI og deres api. 

## Stack navigation
Vi skal bruge en stack navigation til at navigere mellem vores sider.

1. Opret mappen `components` og i den mappe opret en fil ved navn `StackNavigator.js`

Stack navigationen skal have 2 sider, en til vores chat og en til vores home screen.
2. Opret mappen `screens` med følgende filer i:
- ChatScreen.js
- HomeScreen.js

I StackNavigator.js skal vi importere de nødvendige komponenter og oprette en stack navigation med `const Stack=createStackNavigator();`

3. Importer dine screens, createStackNavigator og NavigationContainer
4. Opret stack navigationen `const Stack = createStackNavigator();`
5. Lav selve navigationen
```javascript
export default function HomeNavigation() {
  return (
    <???>
      <???.??? screenOptions={{}}>
        <???.??? name="home" component={HomeScreen}
          options={{ headerShown: false }}
        />
        <???.??? name="chat" component={ChatScreen} />
      </???.???>
    </???>
  );
}
```

### App.js
Vi skal nu tilbage til vores App.js og importere vores StackNavigator. <br>

```javascript
export default function App() {
  return <HomeNavigation />;
}
```

I skal også slette jeres styles i App.js.

**Tip**
Ja din App.js er meget kort i denne øvelse

## GlobalStyles.js
1. Opret mappen `styles` og i den filen `GlobalStyles.js`
2. Indsæt følgende
```javascript
import { StyleSheet } from "react-native";

const GlobalStyles = StyleSheet.create({
  safeArea: {
    flex: 1,
    backgroundColor: "#671ddf",
  },
  mainView: {
    flex: 1,
    backgroundColor: "#fff",
  },
  bubbleRight: {
    backgroundColor: "#671ddf",
  },
  bubbleLeftText: {
    color: "#671ddf",
    padding: 2,
  },
  bubbleRightText: {
    padding: 2,
  },
  inputToolbarContainer: {
    padding: 3,
    backgroundColor: "#671ddf",
    borderTopWidth: 1,
    borderTopColor: "#E8E8E8",
    minHeight: 48,
  },
  inputToolbarText: {
    color: "#fff",
  },
  sendButton: {
    marginRight: 10,
    marginBottom: 5,
  },
  // HomeScreen styles
  homeContainer: {
    flex: 1,
    alignItems: "center",
    justifyContent: "center",
  },
  homeCenter: {
    alignItems: "center",
  },
  homeHello: {
    fontSize: 30,
  },
  homeName: {
    fontSize: 30,
    fontWeight: "bold",
  },
  homeImage: {
    height: 150,
    width: 150,
    marginTop: 20,
  },
  homeHelp: {
    marginTop: 30,
    fontSize: 25,
  },
  homeBotList: {
    marginTop: 20,
    backgroundColor: "#F5F5F5",
    alignItems: "center",
    height: 110,
    padding: 10,
    borderRadius: 10,
  },
  homeBotAvatar: {
    width: 40,
    height: 40,
  },
  homeBotListText: {
    marginTop: 5,
    fontSize: 17,
    color: "#B0B0B0",
  },
  homeChatButton: {
    marginTop: 40,
    padding: 17,
    width: "60%",
    borderRadius: 100,
    alignItems: "center",
  },
  homeChatButtonText: {
    fontSize: 16,
    color: "#fff",
  },
});

export default GlobalStyles;
```
   
## services
1. Lav en mappe kaldet `services` og lav to filer i denne mappe: `ChatFaceData.js` og `Request.js`

### ChatFaceData.js
1. I ChatFaceData.js skal du indsætte følgende kode:
```javascript
// services/ChatFaceData.js
const chatFaceData = [
  { id: 1, name: 'Noyi', image: 'https://res.cloudinary.com/dknvsbuyy/image/upload/v1685678135/chat_1_c7eda483e3.png', primary: '#FFC107', secondary: '' },
  { id: 2, name: 'Pogu', image: 'https://res.cloudinary.com/dknvsbuyy/image/upload/v1685709886/image_21_2e18bb4a61.png', primary: '#E53057', secondary: '' },
  { id: 3, name: 'Nista', image: 'https://res.cloudinary.com/dknvsbuyy/image/upload/v1685709886/image_22_409561b953.png', primary: '#3B96D2', secondary: '' },
  { id: 4, name: 'Estor', image: 'https://res.cloudinary.com/dknvsbuyy/image/upload/v1685709886/image_18_893d24cebc.png', primary: '#37474F', secondary: '' },
  { id: 5, name: 'Pega', image: 'https://res.cloudinary.com/dknvsbuyy/image/upload/v1685709886/image_23_211d7370cb.png', primary: '#2473FE', secondary: '' },
];

export default chatFaceData;
```

### Request.js
```javascript
import OpenAI from "openai/index.mjs";

import { OPENAI_API_KEY } from "@env"; // Vi opretter denne lige om lidt
// Opret en ny instans af OpenAI-klassen
const openai = new OpenAI({ apiKey: OPENAI_API_KEY });

// En lille console.log til at kontrollere at API keyen kan læses
// console.log("OPENAI_API_KEY:", JSON.stringify(OPENAI_API_KEY));

// Funktion der sender en besked til OpenAI API'et
export default async function SendMessage(messageArray) {
  const response = await openai.chat.completions.create({
    model: "gpt-5", // Her vælger du hvilken model du ønsker at bruge
    messages: messageArray,
  });

  // Udtræk AI'ens svar fra svaret
  const result = response.choices[0]?.message?.content || "";
  // Returnér AI'ens svar
  return { role: "assistant", content: result };
}
```

#### .env
1. Da din API er hemmelig, ønsker vi ikke, at den skal stå hard coded i koden. Derfor skal du oprette filen `.env` og tilføje `.env` til din `.gitignore`
2. I `.env` skriver du følgende: `OPENAI_API_KEY=`hvor du så sætter din API kode ind

#### babel.config.js
1. Lav en babel.config.js og indsæt dette:
```javascript
module.exports = {
  presets: ["babel-preset-expo"],
  plugins: [["module:react-native-dotenv"]],
};
```

Forklaring på koden:
Babel er et værktøj, der oversætter moderne JavaScript-kode (ES6+, JSX osv.) til ældre JavaScript, så den kan køre på alle enheder og browsere.
- babel-preset-expo: kommer fra Expo, og indeholder alt det nødvendige for at køre React Native med moderne JavaScript
- module:react-native-dotenv: er det plugin, der lader dig bruge: `import { OPENAI_API_KEY } from "@env";`

**Tip**
Hvis det ikke fungerer, kan du prøve at installere følgende i terminalen
```javascript
npm install babel-preset-expo --save-dev
npm install react-native-dotenv
```

## HomeScreen.js
Før vi kan arbejde med vores chatbot, skal vi have lavet en home screen, hvor vi kan vælge at starte en chat.

1. Importere de nødvendige komponenter og styling:
```javascript
import { useEffect, useState } from "react";
import { View, Text, Image, FlatList, TouchableOpacity } from "react-native";
import AsyncStorage from "@react-native-async-storage/async-storage";
import chatFaceData from "../services/ChatFaceData"; // bemærk lille c da det er data vi importerer
import GlobalStyles from "../styles/GlobalStyles";
```

2. Lav en `HomeScreen` funktion
`export default function HomeScreen({ navigation }) {`

3. Opret to useStates
```javascript
  const [faces] = useState(chatFaceData); // statisk liste
  const [selectedFace, setSelectedFace] = useState(faces[0]); // default valgt bot
```
Dette er en React Hook ved navn useState, der bruges til at oprette en tilstandsvariabel og en funktion til at opdatere denne tilstand.

4. Herefter laves en useEffect til at hente de valgte chatbot. Her gør vi også brug af en asynkron funktion med try-catch
```javascript
useEffect(() => {
    (async () => {
      try {
        // vi gemmer og læser et 0-baseret index (samme som i ChatScreen)
        const idxStr = await AsyncStorage.getItem("chatFaceId");
        const idx = Number.parseInt(idxStr ?? "0", 10);
        const chosen = faces[idx] ?? faces[0];
        setSelectedFace(chosen);
      } catch (e) {
        setSelectedFace(faces[0]);
      }
    })();
  }, [faces]);
```

5. Til sidst laver vi en funktion, der håndterer, at vi kan skifte imellem dem.
```javascript
  const onChatFacePress = async (id) => {
    const idx = faces.findIndex((f) => f.id === id);
    if (idx >= 0) {
      setSelectedFace(faces[idx]);
      await AsyncStorage.setItem("chatFaceId", String(idx));
    }
  };
```

6. Nu skal vi lave en return statement. Den består af tre views.
```javascript
return (
    <View style={GlobalStyles.homeContainer}>
      <View style={GlobalStyles.homeCenter}>
        <View style={GlobalStyles.homeBotList}>
        </View>
      </View>
    </View>
  );
```

7. Indsæt følgende kode i andet view (homeCenter)
```javascript
        <Text
          style={[GlobalStyles.homeHello, { color: selectedFace?.primary }]}
        >
          Hello,
        </Text>

        <Text style={[GlobalStyles.homeName, { color: selectedFace?.primary }]}>
          I am {selectedFace?.name}
        </Text>

        <Image
          source={selectedFace?.image ? { uri: selectedFace.image } : undefined}
          style={GlobalStyles.homeImage}
        />

        <Text style={GlobalStyles.homeHelp}>How Can I help you?</Text>
```
8. Opret en FlatList i tredje view
```javascript
      <???
            data={faces}
            keyExtractor={(item) => String(item.id)}
            horizontal
            renderItem={({ item }) =>
              item.id !== selectedFace?.id && (
                <TouchableOpacity
                  style={{ margin: 15 }}
                  onPress={() => onChatFacePress(item.id)}
                >
                  <Image
                    source={{ uri: item.image }}
                    style={GlobalStyles.homeBotAvatar}
                  />
                </TouchableOpacity>
              )
            }
          />
```

9. Tilføj dette efter flatlisten (i samme view)
```javascript
          <Text style={GlobalStyles.homeBotListText}>
            Choose Your Fav ChatBuddy
          </Text>
```

10. Til slut skal du oprette en knap, som skal være i `homeCenter` viewet
```javascript
    <TouchableOpacity
          style={[
            GlobalStyles.homeChatButton,
            { backgroundColor: selectedFace?.primary },
          ]}
          onPress={() => navigation.navigate("chat")}
        >
          <Text style={GlobalStyles.homeChatButtonText}>Let's Chat</Text>
    </TouchableOpacity>
```

## ChatScreen.js
1. I `ChatScreen.js` skal du importere følgende:
```javascript
import { useRef } from "react";
import { useState, useEffect, useCallback } from "react";
import { View } from "react-native";
import { SafeAreaView } from "react-native-safe-area-context";
import AsyncStorage from "@react-native-async-storage/async-storage";
import { Bubble, GiftedChat, InputToolbar, Send} from "react-native-gifted-chat";

import { FontAwesome } from "@expo/vector-icons";
import GlobalStyles from "../styles/GlobalStyles";

import chatFaceData from "../services/ChatFaceData";
import SendMessage from "../services/Request";

let CHAT_BOT_FACE =
  "https://res.cloudinary.com/dknvsbuyy/image/upload/v1685678135/chat_1_c7eda483e3.png";
```

2. Lav funktionen `export default function ChatScreen() {}`
3. Lav tre useState statements som håndterer vores chat
```javascript
  const [messages, setMessages] = useState([]);
  const [loading, setLoading] = useState(false);
  const [chatFaceColor, setChatFaceColor] = useState();
```

4.  Lav følgende const med useRef. Det er her du prompter den, så du kan evt. prøve at skrive noget andet. Den kan også andre sprog end engelsk.
```javascript
  const conversationHistory = useRef([
    {
      role: "system",
      content:
        "You are an assistant that serves as a tutor for master’s students learning business model theory. You adapt to the student’s knowledge level and guide them to discover answers on their own, avoiding direct solutions. You have a strong understanding of Chesbrough’s core ideas and ensure conversations stay focused on business model theory. Encourage critical thinking and use questions to deepen the student’s understanding. You are not supposed to answer any other questions that are not related to business model theory.",
    },
    {
      role: "assistant",
      content: "Hello, I am your assistant. How can I help you?",
    },
  ]);
```

5. Lav en useEffect til at hente vores valgte chatbot.
```javascript
  //Håndtere valgt ChatBot
  useEffect(() => {
    checkFaceId();
  }, []);
```

6. Lav en const som sætter den valgte chatbot til og sender den første besked. Der skal bruges async.
```javascript
  const checkFaceId = async () => {
    const id = await AsyncStorage.getItem("chatFaceId");
    CHAT_BOT_FACE = id ? chatFaceData[id].image : chatFaceData[0].image;
    setChatFaceColor(chatFaceData[id].primary);
    setMessages([
      {
        _id: 1,
        text: "Hello, I am " + chatFaceData[id].name + ", How Can I help you?",
        createdAt: new Date(),
        user: {
          _id: 2,
          name: "React Native",
          avatar: CHAT_BOT_FACE,
        },
      },
    ]);
  };
```
Kort fortalt: checkFaceId funktionen tjekker, hvilken chatbot der tidligere er blevet valgt (hvis nogen), sætter chatbotens visuelle repræsentation, og initialiserer en introduktionsbesked fra den valgte chatbot.


7. Lav en const som håndterer, når vi sender en besked
```javascript
 const onSend = useCallback((messages = []) => {
    setMessages((previousMessages) =>
      GiftedChat.append(previousMessages, messages)
    );
    if (messages[0].text) {
      getBardResp(messages[0].text);
    }
  }, []);
```

8. Vi skal nu lave en funktion, der håndterer, når vi modtager en besked fra vores bot
```javascript
//Håndtere API Kald og BOT Svar
  const getBardResp = (msg) => {
    setLoading(true);

    //Sætter brugerens besked i vores chat "hukommelse"
    const userMessage = { role: "user", content: msg };
    conversationHistory.current.push(userMessage);

    //SendMessage er vores funktion fra RequestPage.js
    SendMessage(conversationHistory.current)
      .then((response) => {
        if (response.content) {
          setLoading(false);

          //Sætter chat AI's svar i den format gifted chat forventer.
          const chatAIResp = {
            _id: Math.random() * (9999999 - 1),
            text: response.content,
            createdAt: new Date(),
            user: {
              _id: 2,
              name: "React Native",
              avatar: CHAT_BOT_FACE,
            },
          };
          //Sætter chat AI's svar i vores chat "hukkomelse"
          const botMessage = { role: "assistant", content: response.content };
          conversationHistory.current.push(botMessage);

          //Sætter chat AI's svar i vores chat
          setMessages((previousMessages) =>
            GiftedChat.append(previousMessages, chatAIResp)
          );
        } else {
          //Hvis der ikke er et svar fra AI
          setLoading(false);

          const chatAIResp = {
            _id: Math.random() * (9999999 - 1),
            text: "Sorry, I cannot help with it",
            createdAt: new Date(),
            user: {
              _id: 2,
              name: "React Native",
              avatar: CHAT_BOT_FACE,
            },
          };

          setMessages((previousMessages) =>
            GiftedChat.append(previousMessages, chatAIResp)
          );
        }
      })
      .catch((error) => {
        console.error(error);
        // Handle error further if needed
      });
  };
```
Funktionen getBardResp tager en parameter msg. Når den kaldes, sætter den tilstanden loading til true, hvilket viser en loading animation i brugerfladen. Derefter kalder den SendMessage funktionen fra RequestPage.js med msg som parameter. Denne funktion kan blive brugt, når en bruger sender en besked i en brugerflade, hvorefter beskeden bliver sendt til OpenAI API'en.

Når API'en svarer, sætter funktionen loading til false, hvilket fjerner loading animationen fra brugerfladen. Derefter tjekker den, om API'en har svaret med en besked (response.data.BOT). Hvis den har, opretter den en besked med svaret fra API'en og tilføjer den til beskedhistorikken. Hvis den ikke har, opretter den en besked med en standardfejlmeddelelse og tilføjer den til beskedhistorikken.

9. Vi laver nu en funktion, der laver en bobble til vores chat
```javascript
  const renderBubble = (props) => {
    return (
      <Bubble
        {...props}
        wrapperStyle={{
          right: GlobalStyles.bubbleRight,
        }}
        textStyle={{
          right: GlobalStyles.bubbleRightText,
          left: GlobalStyles.bubbleLeftText,
        }}
      />
    );
  };
```

10. Vi laver nu en funktion, der laver en toolbar til vores chat
```javascript
 const renderInputToolbar = (props) => {
    return (
      <InputToolbar
        {...props}
        containerStyle={GlobalStyles.inputToolbarContainer}
        textInputStyle={GlobalStyles.inputToolbarText}
        textInputProps={{
          editable: true,
          placeholder: "Type a message...",
          placeholderTextColor: "#eee",
        }}
      />
    );
  };
```

11. Vi laver nu en funktion, der laver en send knap til vores chat
```javascript
  const renderSend = (props) => {
    return (
      <Send {...props}>
        <View style={GlobalStyles.sendButton}>
          <FontAwesome
            name="send"
            size={24}
            color="white"
            resizeMode={"center"}
          />
        </View>
      </Send>
    );
  };
  ```

12. Til sidst skal vi nu lave en return funktion, der indeholder vores chat
```javascript
 return (
    <SafeAreaView style={GlobalStyles.safeArea}>
      <View style={GlobalStyles.mainView}>
        <GiftedChat
          messages={messages}
          isTyping={loading}
          multiline={true}
          onSend={(messages) => onSend(messages)}
          user={{
            _id: 1,
          }}
          renderBubble={renderBubble}
          renderInputToolbar={renderInputToolbar}
          renderSend={renderSend}
        />
      </View>
    </SafeAreaView>
  );
```
ChatScreen.js skulle nu gerne være done

# Fil Checkliste
- [ ] `StackNavigator.js`
- [ ] `App.js`
- [ ] `HomeScreen.js`
- [ ] `ChatScreen.js`
- [ ] `ChatFaceData.js`
- [ ] `Request.js`
- [ ] `.env`
- [ ] `babel.config.js`
- [ ] `GlobalStyles.js`
- [ ] `.gitignore`

Fedt nu skulle det gerne virke! STÆRKT! Du kan lege med styling eller prøve at ændre promptet

