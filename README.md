# xurei Messenger Chatbot

Simple Messenger chatbot framework for simple Q&A conversations based on Finite State Machine.

**This is a work in progress ! Expect things to be broken**

### Usage
The chatbot works by setting the different questions and the answers that it expects.
Questions are linked together via a simple FSM, i.e. by changing state of the current session.

Here is an example of question :
```
"REQUEST_DATE": {
    execute: function(api, session) {
        return api.sendTextMessage(session.senderId(), "When is the meeting taking place (dd/mm/yyyy) ?");
    },
    answers: {
        "INPUT": function (api, session, payload) {

            if (is_date_valid(payload.text)) {
                //Store information in the user session
                session.store.date = payload.text;
                //Change the state to the next question
                session.setState("REQUEST_BUDGET");
            }
            else {
                //Send the invalid data message
                api.sendTextMessage(session.senderId(), "Sorry, I didn't get it.")
                //Change the state to itself so it can ask the question again
                .then(() => session.setState("REQUEST_DATE"));
            }
        }
    }
},
```

```
const xchatbot = require('../../xchatbot/xchatbot');

const chatbot = xchatbot({
	validation_token: "hello_im_spleety_$ù^*",
	access_token: "EAACzy9ahy38BAO7PNrCHcDpwlGVYgZBkBWweEZAQmRT07aoefXjzk2GTbweOHxv5h7nTmRhOYQgCAW9xgmKIVwlZCUvnDonWGkEwF2aFKP5UbxZBNeyFBZBjZBnMFrjc6WGAQYNuvDi0rBxiREQoHbeZCowLPuNdoFzNgzW40kVpAZDZD"
});
const app = chatbot.express;

chatbot.api.setMenu({
	"setting_type" : "call_to_actions",
	"thread_state" : "existing_thread",
	"call_to_actions":[
		{
			"type":"postback",
			"title":"Help",
			"payload": JSON.stringify({action: "HELP"})
		},
		{
			"type":"postback",
			"title":"Create new request",
			"payload": JSON.stringify({action: "CREATE_REQUEST"})
		},
		{
			"type":"web_url",
			"title":"View Website",
			"url":"http://www.google.com/"
		}
	]
});
```