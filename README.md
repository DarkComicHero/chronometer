Alexa is finally defeated in our house.

All our Alexa devices are plugged into HA-controlled switches. Through our ESPHome voice assistants, we turn them on with “We miss Alexa” and off with “Make Alexa go away.”

At first, we were constantly turning them back on for named timers. Those have been the most-used Alexa commands in our house since the beginning, especially when cooking.

I finally have a solution everyone likes, and Amazon hasn’t been up in our business for weeks. The setup is a little time-consuming, but maybe someone smarter than me can turn this into a custom integration available through HACS. If not, maybe this will help someone else.

https://github.com/DarkComicHero/chronometer

**Our requirements:**

- Set multiple named timers using ESPHome voice devices and announce each timer’s name when it finishes. For example, a “60-minute chicken timer” and a “30-minute potato timer.”
- Announce completion on more than just the device that started the timer. For example, start a timer in the kitchen and hear it in the family room so I can go back to watching TV. With Alexa, we’d tell the kitchen Echo, “Set a 35-minute green bean timer on Family Alexa.” My solution announces on all devices, which works better for us anyway.
- Show timers on the dashboard and allow us to adjust them there.
- Adjust timers while they’re running. For example, the voice assistant heard “tomato timer for 3 minutes,” but I meant “potato timer for 30 minutes.”
- Survive a reboot of HA or an ESPHome device.
- Ask how much time remains. This one still isn’t resolved through voice, but the remaining time is visible on the dashboard.

**My setup:**

- Six ESPHome devices: a mix of Satellite1, Third Reality Voice, and homebrew devices.
- Three always-on dashboards.
- A local LLM for the assistant.

And, full disclosure, I’m a Star Trek geek, so my entire HA setup has a Star Trek theme. LCARS-themed dashboards, devices named things like “Bridge Life Support” for the office thermostat and “Shields” for Alarmo, starting my coffee pot with “Earl Grey tea, hot,” etc.

I tried a number of integrations available through HACS that support named timers:

- None I found announced the timer’s name when it finished.
- Adding them to a dashboard was frustratingly complicated, and I never got everything working completely.
- None I found let me start a timer on one device and have it announce completion on another ESPHome device.

Through experimentation, I found that using “timer,” “reminder,” or “alert” in my custom commands caused confusion with the built-in handling of those terms. I went with “chronometer” because it fits the Star Trek theme (sort of) and wasn’t easily confused with something else by voice.

I also added “hippopotamus” as an alternative for my kid, since they roll their eyes at the whole Star Trek thing. No idea why they chose it, but it’s a word I’d never use as a command, and they remember it, so I rolled with it.

**Step 1: Helpers**

I created:

- `input_number.chronometer_alpha_length`
- `input_text.chronometer_alpha_name`
- `input_boolean.chronometer_alpha_power`

Then I repeated this for bravo, charlie, delta, and echo, giving me five timer slots and 15 helpers total.

*I initially tried “chronometer 1,” “chronometer 2,” etc., but found that voice commands confused the slot number with the timer’s duration.*

**Step 2: Dashboard**

I created a dashboard card for each slot, alpha through echo, using duplicate, find, and replace.

[[Dashboard](https://github.com/DarkComicHero/chronometer/blob/c5b2401efd29a7f51e5a343680861c8e95b01e66/Dashboard)]

*Again, this uses an LCARS theme.*

**Step 3: Trigger automation**

I created a trigger automation for each slot, alpha through echo, using duplicate, find, and replace.

Then I tested each one from the dashboard by setting its label and duration and turning on its power switch.

[[Trigger automation](https://github.com/DarkComicHero/chronometer/blob/c5b2401efd29a7f51e5a343680861c8e95b01e66/Trigger%20automation)]

*If more than one timer finishes at the same time, the announcements can make the ESPHome devices sound a little nuts. I staggered the trigger seconds: 1, 11, 21, etc. for alpha; 2, 12, 22, etc. for bravo; and so on. That helped somewhat when multiple timers were announcing at once, especially with short labels.*

**Step 4: “Engage” automation**

This sets a named timer through a voice command. It basically parses the duration and label from the command, then uses nested IF/THEN logic to find the next available slot.

Easy concept, but this was probably the most time-consuming piece to get working.

[[Engage automation](https://github.com/DarkComicHero/chronometer/blob/c5b2401efd29a7f51e5a343680861c8e95b01e66/Engage%20automation)]

**Step 5: Adjustment automations**

I created voice-command automations to:

- Set chronometer alpha’s length.
- Set chronometer alpha’s label.

Then I repeated those for bravo through echo using duplicate, find, and replace.

[[Set Chronometer Alpha Length](https://github.com/DarkComicHero/chronometer/blob/c5b2401efd29a7f51e5a343680861c8e95b01e66/Set%20Chronometer%20Alpha%20Length)]

[[Set Chronometer Alpha Name](https://github.com/DarkComicHero/chronometer/blob/c5b2401efd29a7f51e5a343680861c8e95b01e66/Set%20Chronometer%20Alpha%20Name)]

*We've found ourselves adjusting more from the dashboard then using voice, but either does work.*
*I’d like to find a way to adjust timers by name instead of by slot. For example, “Set chronometer roast beef to 65 minutes” or “Add 5 minutes to my potato chronometer.” That seems to be beyond my current skill level.*
