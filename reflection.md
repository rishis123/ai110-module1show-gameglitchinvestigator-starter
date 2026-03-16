# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?
- List at least two concrete bugs you noticed at the start  
  (for example: "the secret number kept changing" or "the hints were backwards").

---

The hints seem broken -- e.g., when the secret number was 99 and I guessed 90, it instructed me to go lower. Same for when I guessed 100 sometimes. 

Also it seems like the new game button is sometimes broken, like maybe if you run out of guesses or win a game, and it just gives the error message to make a new game.


## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?
- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).

---

I used Github Copilot and Claude Code

1) 
Regarding the first bug above, it gave me this response:

Hint text is flipped.
When guess > secret the code returns "Too High" but tells the user “Go HIGHER!” – so a correct “too high” outcome sends the wrong directional hint. Likewise the "Too Low" branch says “Go LOWER!”.

Secret occasionally becomes a string.
The game alternates between storing secret as an int and a str (see the if st.session_state.attempts % 2 == 0: secret = str(...) in the submit block). When secret is a string the first guess > secret comparison raises a TypeError and the code falls into the except block, which then compares the stringified guess lexicographically.

'90' > '99' is False, so a guess of 90 against secret '99' is treated as “Too Low” – but the message it returns is the already‑swapped “📉 Go LOWER!”.
This also explains why guesses like 100 sometimes behave oddly: string comparison '100' > '99' is True, so 100 is “Too High” even if numerically it isn’t, and the hint text is wrong too.

Observing the check_guess function and submit-handling logic, it seems right. Every second guess, the secret is converted from an integer to a string, which throws an error in check_guess and converts the guess to a string to compare two strings. Comparing 2 strings has differences lexicographically than comparing 2 numbers.


Claude gave me the following incorrect point: 
''100' > '99' is True (because '1' > '9' is False? actually '1' < '9' so wait string comparison gives False— but maybe other examples)… the core point is lex order is wrong.' I checked online to confirm, and it seems like 100 will come before 99 lexicographically but obviously not numerically, so Claude had a good example initially but it changed its mind.


2) 
For the second bug above, Claude gave me the below snippet:

if new_game:
    st.session_state.attempts = 0
    st.session_state.secret = random.randint(1, 100)
    st.success("New game started.")
    st.rerun()

if st.session_state.status != "playing":
  if st.session_state.status == "won":
      st.success("You already won. Start a new game to play again.")
  else:
      st.error("Game over. Start a new game to try again.")
  st.stop()

Claude explained that clicking new game only resets attempts and the secret number, but never clears status, score, or history, so the solution is to reset all of the game-state fields when you start a new game. 



## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
- Describe at least one test you ran (manual or using pytest)  
  and what it showed you about your code.
- Did AI help you design or understand any tests? How?

---

## 4. What did you learn about Streamlit and state?

- In your own words, explain why the secret number kept changing in the original app.
- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?
- What change did you make that finally gave the game a stable secret number?

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.
