# 세번째 포스팅

![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)

코드를 테스트 하던 도중 재미난 것을 발견해서 적어봅니다

---

## Closure & context

일반적으로 Clousre이라 함은 함수 내부에 또다른 함수가 존재하는 형태를 말합니다.  
이런 형태가 나오게 된 것은 javascript의 혼돈의 카오스적인 시기를 반영한 이유가 큽니다. 예를 들면

---

## Features

- Material Design
- Emoji support
- User @mentioning
- Private messaging
- Message deleting (for admins)
- Ability to kick/ban users (for admins)
- See other user's IPs (for admins)
- Other awesome features yet to be implemented

.
![User Features](http://i.imgur.com/WbF1fi2.png)

.
![Admin Features](http://i.imgur.com/xQFaadt.png)

#### There are 3 admin levels:

- **Helper:** Can delete chat messages
- **Moderator:** The above plus the ability to kick and ban users
- **Administrator:** All the above plus send global alerts and promote/demote users

---

## Setup

Clone this repo to your desktop and run `npm install` to install all the dependencies.

You might want to look into `config.json` to make change the port you want to use and set up a SSL certificate.

---

## Usage

After you clone this repo to your desktop, go to its root directory and run `npm install` to install its dependencies.

Once the dependencies are installed, you can run `npm start` to start the application. You will then be able to access it at localhost:3000

To give yourself administrator permissions on the chat, you will have to type `/role [your-name]` in the app console.

---

## License

> You can check out the full license [here](https://github.com/IgorAntun/node-chat/blob/master/LICENSE)

This project is licensed under the terms of the **MIT** license.
