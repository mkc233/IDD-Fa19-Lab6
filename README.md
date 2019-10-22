# ChatBot

*A lab report by Michael Chan*

## In this Report

To submit your lab, fork [this repository](https://github.com/FAR-Lab/IDD-Fa18-Lab6). You'll need to upload any code you change into your fork, as well as upload a video of a friend or classmate using your chatbot.

## Make the ChatBot your own

**Describe what changes you made to the baseline chatbot here. Don't forget to push your modified code to this repository.**

I changed the bot to become a Cornell Tech information bot, it talks to the user and asks them what program they are interested in.  It that asks them some more general questions about where they are from and when they plan to apply in order to provide some more information about Cornell Tech.

```
/*
chatServer.js
Author: David Goedicke (da.goedicke@gmail.com)
Closley based on work from Nikolas Martelaro (nmartelaro@gmail.com) as well as Captain Anonymous (https://codepen.io/anon/pen/PEVYXz) who forked of an original work by Ian Tairea (https://codepen.io/mrtairea/pen/yJapwv)
*/

var express = require('express'); // web server application
var app = express(); // webapp
var http = require('http').Server(app); // connects http library to server
var io = require('socket.io')(http); // connect websocket library to server
var serverPort = 8000;


//---------------------- WEBAPP SERVER SETUP ---------------------------------//
// use express to create the simple webapp
app.use(express.static('public')); // find pages in public directory

// start the server and say what port it is on
http.listen(serverPort, function() {
  console.log('listening on *:%s', serverPort);
});
//----------------------------------------------------------------------------//


//---------------------- WEBSOCKET COMMUNICATION -----------------------------//
// this is the websocket event handler and say if someone connects
// as long as someone is connected, listen for messages
io.on('connect', function(socket) {
  console.log('a new user connected');
  var questionNum = 0; // keep count of question, used for IF condition.
  socket.on('loaded', function() { // we wait until the client has loaded and contacted us that it is ready to go.

    socket.emit('answer', "Hello, I am Cornell Tech's admission bot.  I can help you with any questions you have about one of our programs"); //We start with the introduction;
    setTimeout(timedQuestion, 5000, socket, "Would you like to hear about our programs? Please press enter"); // Wait a moment and respond with a question.
  });
  socket.on('message', (data) => { // If we get a new message from the client we process it;
    console.log(data);
    questionNum = bot(data, socket, questionNum); // run the bot function with the new message
  });
  socket.on('disconnect', function() { // This function  gets called when the browser window gets closed
    console.log('user disconnected');
  });
});
//--------------------------CHAT BOT FUNCTION-------------------------------//
function bot(data, socket, questionNum) {
  var input = data; // This is generally really terrible from a security point of view ToDo avoid code injection
  var answer;
  var question;
  var waitTime;
  var program;

  /// These are the main statments that make up the conversation.
  if (questionNum == 0){
      answer = "Awesome! Please select which program you want to hear about"
      waitTime = 5000;
      question = "1)Connective Media 2)Health Tech 3)Tech MBA 4)Computer Science 5)Electrical and Computer Engineering 6)Operations Research and Information Engineering 7)Law Technology and Entrepreneurship";
  }
  else if (questionNum == 1) {
      if (input == 1){
        answer = "The Connective Media program is a 2 year dual masters of science degree in applied information science and information systems.  This program is a comptuer science focused curriculum centered on the ecosystems formed when communication, information and media converge"; // output response
        waitTime = 5000;
        question = 'Would you like to hear more?'; // load next question
    }
      else if (input == 2){
        answer = "The Health Tech program is a 2 year masters degree program in building digital health technologies.  There is a three part curriculum with technical courses, health courses and studio courses (these are cross functional application based courses) "; // output response
        waitTime = 5000;
        question = 'Would you like to hear more?'; // load next question
      }
      else if (input == 3){
        answer = "The Johnson Cornell Tech MBA program is a 1 year Masters in Busineess Adminstration with a focus on the tech industry.  The curriculum consists of a 3 months of intense business classes up at the Cornell Ithaca campus, and 9 months at Cornell Tech working in studio."; // output response
        waitTime = 5000;
        question = 'Would you like to hear more?'; // load next question
      }
      else if (input == 4){
        answer = "The masters of engineering in computer science is a 1 year Masters of engineering.  You will we have a rigorous but flexbile curriculum of technical courses to use towards developing future technologies."; // output response
        waitTime = 5000;
        question = 'Would you like to hear more?'; // load next question
      }
      else if (input == 5){
        answer = "The masters in eletrical and computer engineering is a 1 year program where students will protoypte new hardware and improve advanced algorithms.  A combination of innovative laboratory projects and rigourously taught theoretical concepts will propel students to success in the future."; // output response
        waitTime = 5000;
        question = 'Would you like to hear more?'; // load next question
      }
      else if (input == 6){
        answer = "The ORIE program is a 1 year masters program where students will learn about the importance of data in the digital age, and teach students how to use that data to make insightful, real-time business decisions."; // output response
        waitTime = 5000;
        question = 'Would you like to hear more?'; // load next question
      }
      else if (input == 7){
        answer = "The LLM program is a 1 year Masters of Law program where students will learn how the rapid evolution of technology is affecting existing laws, and how new regulations will need to be formulated.  Prospective students must be licensed lawyers."; // output response
        waitTime = 5000;
        question = 'Would you like to hear more?'; // load next question
      }
  } else if (questionNum == 2){
      if (input.toLowerCase() == "yes" || input.toLowerCase() == "y"){
        answer = "Please check out our website at https://tech.cornell.edu/ for more detailed information on all the programs" // output response
        waitTime = 5000;
        question = 'When do you plan on applying?'; // load next question
  }
      else {
        answer = "Thats too bad!  Cornell Tech is a great place to study! Maybe you want to learn more about our other programs";
        waitTime = 5000;
        question = "1)Connective Media 2)Health Tech 3)Tech MBA 4)Computer Science 5)Electrical and Computer Engineering 6)Operations Research and Information Engineering 7)Law Technology and Entrepreneurship"
        questionNum = questionNum - 2
  }
} else if (questionNum == 3){
    if(input > 2022){
      answer = "You still have a lot of time to apply!  Make sure to get you recommendations and test scores ready!"
      waitTime = 5000;
      question = "Where are you from?"
  }
    else if(input < 2019){
      answer = "That date is in the past! Please enter a real date";
      waitTime = 5000;
      question = "When do you plan on applying?"
      questionNum--
    }
    else{
      answer = "You don't have much time left! Make sure to get all your materials ready ASAP"
      waitTime = 5000;
      question = "Where are you from"
    }
} else if (questionNum == 4){
    if(input.toLowerCase() == "new york" || input.toLowerCase() == "ny" || input.toLowerCase() == "nyc" || input.toLowerCase() == "new york city" ){
      answer = "Wow you live so close!  Come check out the campus sometime, we host tours every week"
      waitTime = 5000;
      question = "Did you find this bot to be helpful?"
    }
    else{
      answer = "I have never heard of that place before!  But if you ever have time come checkout NYC and the beautiful campus we have";
      waitTime = 5000;
      question = "Did you find this bot to be helpful?"
    }
} else if (questionNum == 5){
  if(input.toLowerCase() == "yes" || input.toLowerCase() == "y"){
    answer = "Thank you, Im glad you found this helpful!"
    waitTime = 5000;
  }
  else if (input.toLowerCase() == 'no' || input.toLowerCase() == 'n'){
    answer = "Sorry :( we will continue to try to improve our bots performance"
    waitTime = 5000;
  }
}

else {
    answer = 'I hope you found the information you were looking for!'; // output response
    waitTime = 0;
    question = '';
}


  /// We take the changed data and distribute it across the required objects.
  socket.emit('answer', answer);
  setTimeout(timedQuestion, waitTime, socket, question);
  return (questionNum + 1);
}

function timedQuestion(socket, question) {
  if (question != '') {
    socket.emit('question', question);
  } else {
    //console.log('No Question send!');
  }

}
//----------------------------------------------------------------------------//
```

## Record someone trying out your ChatBot

**Using a phone or other video device, record someone trying out your ChatBot. Upload that video to this repository and link to it here!**

---
Starter code by [David Goedicke](mailto:da.goedicke@gmail.com), closely based on work by [Nikolas Martelaro](mailto:nmartelaro@gmail.com) and [Captain Anonymous](https://codepen.io/anon/pen/PEVYXz), who forked original work by [Ian Tairea](https://codepen.io/mrtairea/pen/yJapwv).
