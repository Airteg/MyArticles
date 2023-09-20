# Стилізація коду в React за допомогою бібліотеки @emotion.

## Пекет @emotion/css

Цей пакет **_не залежить_** від фреймворку. Може працювати з чистим JS.
Встановлюється:

```
npm i @emotion/css
```

Використання:

```
const app = document.getElementById('root');
const myStyle = css`color: rebeccapurple;`;
app.classList.add(myStyle);
```

_(Додаткова інформація в документації на [сайті фреймворку](https://emotion.sh/docs/@emotion/css) чи на [сайті npm](https://www.npmjs.com/package/@emotion/css))_

### 2. Використання в React.

```
import { css } from '@emotion/css'
<div className={css`font-family: sans-serif; text-align: center;`} >
```

В цьому випадку `@emotion/css` присвоїв `div` випадковий клас (див. DevTools), наприклад, `class="css-f9g3kc"` і помістив в `<head>` такий запис:

```
<style data-emotion="css" data-s="">
    .css-f9g3kc{
        font-family:sans-serif;
        text-align:center;}
</style>
```

**Використовуючи цю бібліотеку, ми можемо додавати стилі через змінні:**

```
const bgColor = 'lightgreen';
const appClass = `
    font-family: sans-serif;
    text-align: center;
    width: 100vw;
    height: 100vh;
    background-color: ${bgColor};
`;
...
return <div className={css`${appClass}`} {children}/>
```

Або навіть більше. Наприклад, в цьому випадку ми можемо в стилі прокидувати пропси:

```
const bgColor = 'lightyellow';
const appClass = (props) => {
  return `
  font-family: sans-serif;
  text-align: center;
  width: 100vw;
  height: 100vh;
  background-color: ${props};`;
};
// ... якийсь код
return <div className={css`${appClass(bgColor)}`}/>
```

## css Prop.

**_Примітка: Для використання цього способу з create-react-app потрібні додаткові налаштування dev-середовища!_**

Основний спосіб емоційно стилізувати елементи – це `css Prop`. Він надає стислий і гнучкий API для стилізації ваших компонентів.

### Встановлення.

Для стилізаваних компонентів встановлюємо бібліотеки:

```
npm i @emotion/styled @emotion/react @emotion/babel-plugin
```

Після встановлення у нас вже є можливість створювати стилізовані компоненти. І про це пізніше.

Тож продовжимо установку.

Як відомо, Браузери не розуміють JSX з коробки, тому більшість користувачів React покладаються на такі компілятори, як `Babel` або `TypeScript`. Для `css Prop` потрібні спеціальні налаштування `Babel`, особливо для додатків, створених за допомогою `create-react-app`.

У `create-react-app` свій підхід до конфігурації початкового стану. В `package.json` після створення додатку стисла інформація, яка не видна програмісту. Але є скрипт `"eject": "react-scripts eject"`, після виконання якого всі ці налаштування, та пакети стають видимими. Тож тільки після виконання цього скрипта ми можемо продовжити налаштування Babel, щоб працювала фіча `css Prop`.
Отож після того як були встановлені пакети `@emotion/styled`, `@emotion/react` та `@emotion/babel-plugin`, виконаний скрипт `"eject": "react-scripts eject"`, встановлюємо бібліотеку:

```
yarn add @emotion/babel-preset-css-prop
```

і руками додаємо в `package.json` налаштування babel та plugins:

```
"babel": {
  "presets": [
    "react-app",
    "@emotion/babel-preset-css-prop"
  ]
},
...
"plugins": [
  "@emotion"
],
```

І, як виявилось, це не все. В мене все ще ця фіча не працювала. Потім при компіляції додатку була підказка встановити в `package.json` в розділ `devDependencies` ще одну бібліотеку `@babel/plugin-proposal-private-property-in-object`:

```
"devDependencies": {
    "@babel/plugin-proposal-private-property-in-object": "7.21.0",
    "@babel/runtime": "7.13.8",
    "typescript": "4.1.3"
  },
```

Після цього `css Prop` почала працювати в повному обсязі.

---

**_Зауваження._**
**Хочу наголосити, що це все стосується створенню додатка за допомогою `create-react-app`. В `Gatsby` чи `Next` ці питання не досліджувалися мною. Хоча я викоистовував `@emotion` з `Gatsby` на протязі двох років. Але я там приходив в команду після розгортання додатків і не брав участь в розгортанні фреймворків.**

---

## Застосування `css Prop`.

Для демонстрації `css Prop` розглянемо компонент `Button`.

```
import { css } from "@emotion/react";

function Text({ children }) {
  return (
    <span css={css`font-size: 3rem; color: blue;`}>
      {children}
    </span>
  );
}

const Button = ({ children }) => {
  return (
    <button><Text>{children}</Text></button>
  );
};
export default Button;
```

1. Імпортуємо `css` з `@emotion/react`, а не з `@emotion/css`.
2. Створюємо компонент `Text`. `Text` повертає стилізований `span`.
3. Стилі з `span` через компонент `<Text>` застосовуються до `{children}` в Button. Лаконічно і логічно.

## Styled Components.

Створення `staled components` дуже схоже на те, як це робиться в бібліотеці `styled-components`. Та трохи відрізняється те, що відбувається під капотом.
Для наочності давайте розглянемо код:

```
import { css } from "@emotion/react";
import styled from "@emotion/styled";

function Text({ children, theme }) {
  return (
    <span
      css={css`
        font-size: 3rem;
        color: ${theme === "dark" ? "#32cd32" : "#cd32cd"};
      `}
    >
      {children}
    </span>
  );
}

const StyledButton = styled.button`
  background-color: ${(props) => props.theme === "dark" ? "#cd32cd" : #32cd32"};
  border-radius: 1rem;

  &:active {
    opacity: 0.5;
  }
`;

const Button = ({ children, theme = "dark" }) => {
  return (
    <StyledButton theme={theme}>
      <Text theme={theme}>{children}</Text>
    </StyledButton>
  );
};
export default Button;

```

### Передача `theme` в `StyledButton`:

`styled` компоненти в `@emotion/styled` мають доступ до `props`, тому ми можемо використовувати функцію для динамічного визначення стилів на основі `props`. У нашому випадку, ми передаємо `theme` як `prop` в `StyledButton`, і використовуємо цей `prop` для визначення кольору фону.

### Передача `theme` в `Text`:

У `Text` компоненті, ми використовуємо `css props` з `@emotion/react`, який також може використовувати `props`, але у нашому випадку ми просто передаємо `theme` напряму в `Text` компонент і використовуємо його для визначення кольору тексту.

### Таким чином,

`theme` передається від `Button` до `StyledButton` і `Text` через `props`. `Button` компонент приймає `theme` і передає його до `StyledButton` і `Text`.

Для `StyledButton`, `theme` передається як `prop`, і використовується у функції, яка визначає `background-color`.

Для `Text`, `theme` передається напряму в компонент, і використовується у `css props` для визначення кольору тексту.

## Global.

Пакет `@emotion/react` включає утиліту для встановлення глобальних стилів: `Global`. Цей компонент дозволяє нам визначати глобальні стилі, які застосовуються до всього вашого додатку.

Створимо `Global` компонент для встановлення глобальних стилів:

```
import { Global, css } from '@emotion/react'
// Створюємо глобальні стилі:
const globalStyles = css`
  body {
    margin: 0;
    padding: 0;
    font-family: Arial, sans-serif;
  }
`
// В Global компонент передайємо глобальні стилі через styles проп.
function App() {
  return (
    <>
      <Global styles={globalStyles} />
      // ... інші компоненти
    </>
  )
}
```

У цьому прикладі, глобальні стилі, які ви визначили, будуть застосовані до всього додатку. Ви можете включати будь-які глобальні стилі, які вам потрібні, такі як визначення шрифтів, скидання стилів, кольори тла тощо.

Існує декілька загальних правил та кращих практик, яких варто дотримуватися:

- _Розміщення_: Хоча ви теоретично можете розмістити Global компонент будь-де у вашому додатку, краще розміщувати його на найвищому рівні вашого додатку, наприклад у кореневому компоненті (наприклад, App). Це гарантує, що глобальні стилі застосовуються одразу після завантаження додатку.

- _Один раз_: Краще використовувати Global компонент тільки один раз у вашому додатку, щоб уникнути спроб встановити конфліктуючі глобальні стилі в різних місцях.

- Уникайте _динамічних_ стилів: Компонент Global використовується для встановлення глобальних стилів, які, як правило, не мають змінюватися динамічно. Якщо вам потрібно динамічно змінювати стилі, краще використовувати звичайні стилі або використовувати CSS-змінні.

Незважаючи на ці правила, технічно нічого не заважає вам розмістити Global компонент в дочірньому компоненті або використовувати його кілька разів. Однак це може призвести до неочікуваних результатів і складніше керувати, тому це не рекомендується.

## Теми

В React та Emotion ви можете використовувати контекст для задання теми у всьому вашому додатку.

1. **Створення теми:** Спершу вам потрібно створити об'єкт, який буде представляти вашу тему. Це може бути простий об'єкт JavaScript з кольорами, шрифтами та іншими значеннями, які ви хочете використовувати у вашому додатку.

   ```javascript
   const theme = {
     colors: {
       primary: "#ff6347",
       secondary: "#32cd32",
     },
     fontSizes: {
       small: "1rem",
       medium: "2rem",
       large: "3rem",
     },
   };
   ```

2. **Використання `ThemeProvider`:** Emotion включає компонент `ThemeProvider`, який дозволяє вам передати тему через контекст до всіх ваших компонентів.

   Ви можете обгорнути весь ваш додаток (або будь-яку частину додатку, де ви хочете використовувати тему) в `ThemeProvider` і передати вашу тему через `theme` проп.

   ```javascript
   import { ThemeProvider } from "@emotion/react";

   function App() {
     return <ThemeProvider theme={theme}>// your components</ThemeProvider>;
   }
   ```

3. **Використання теми в компонентах:** Всі ваші компоненти, які знаходяться всередині `ThemeProvider`, тепер мають доступ до теми через `props`.

   Ви можете використовувати значення з теми у вашому CSS.

   ```javascript
   const StyledComponent = styled.div`
     background-color: ${(props) => props.theme.colors.primary};
     font-size: ${(props) => props.theme.fontSizes.medium};
   `;
   ```

   Компонент `StyledComponent` буде мати фоновий колір та розмір шрифту визначені в вашій темі.

Це основний спосіб використання тем у Emotion та React. Ви можете задати які завгодно значення у вашій темі та використовувати їх у всіх ваших компонентах.

### useTheme

`useTheme` є хуком від Emotion, який дозволяє вам використовувати вашу тему безпосередньо у вашому компоненті без потреби передачі її через `props`.

Це може бути особливо корисним, якщо ви не використовуєте стилізовані компоненти або якщо вам потрібно доступ до теми поза областю стилів.

Ось як ви можете використовувати `useTheme`:

1. **Завантажте тему за допомогою `ThemeProvider`:**

   Так само, як я вже показав, вам потрібно завантажити вашу тему в контекст за допомогою `ThemeProvider`.

   ```javascript
   <ThemeProvider theme={theme}>// your components</ThemeProvider>
   ```

2. **Використання `useTheme` хука:**

   В будь-якому компоненті, який знаходиться всередині `ThemeProvider`, ви можете використовувати `useTheme` хук, щоб отримати поточну тему.

   ```javascript
   import { useTheme } from "@emotion/react";

   const MyComponent = () => {
     const theme = useTheme();
     return (
       <div style={{ backgroundColor: theme.colors.primary }}>
         Привіт, світ!
       </div>
     );
   };
   ```

У цьому прикладі, `MyComponent` використовує `useTheme` хук, щоб отримати поточну тему і встановити колір фону `div`.

`useTheme` дозволяє вам використовувати тему в будь-якій частині вашого компоненту, не тільки в стилях. Наприклад, ви можете використовувати тему для зміни поведінки компонента в залежності від поточної теми.
