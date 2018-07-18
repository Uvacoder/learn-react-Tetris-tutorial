---
title: "React Redux Tetris - Connect Message Popup"
slug: react-redux-tetris-
---

Connect the message popup to state. 

# Introduction 

The message popup should display when the game is
paused or over. Otherwise it will be invisible. 

Currently it displays all the time. The goal here 
is to grab `isRunning` and `gameOver` properties 
from state and use these to display or hide the 
popup. 

## Challenges

**Import Connect**

Import connect from 'react-redux'. 

**Map State to Props**

Map the `isRunning` and `gameOver` properties from 
state to props in the `mapStateToProps` function. 

**Map Dispatch to Props**

Define the `mapDispatchToProps` function. Return 
an empty object. We won't use this here but we can 
stub it in for future use. 

**Connect the component**

Connect the component with props and dispatch. 

**Hide and show the MessagePopup**

Use the following logic to hide and show the message popup. 

- If `isRunning` and `gameOver` is `false` the message popup
should be hidden. Hide it by adding the `hidden` class to 
`className`. 
- If `isRunning` is `false` the message popup should be visible
and the message text should say 'Paused'. 
- If `gameOver` is `true` the message popup should be visible
and the message text should say 'Game Over'.

## Conclusion


## Resources

 