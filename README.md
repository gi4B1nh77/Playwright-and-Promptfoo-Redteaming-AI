# Redteam AI using Promptfoo intergrate with Playwright
An end-to-end security evaluation framework that simulates real-world adversary behavior. By integrating Promptfoo for automated prompt injection and database management with Playwright for browser-based interaction, the system bypasses traditional API testing to evaluate the AI's safety and robustness directly through the final user interface.
Reference: https://www.promptfoo.dev/docs/installation/

Người Việt bay mà không cần cánh: GiaBinh 

Hosting on Ubuntu 22.04

## 1.	Initial Configuration
### 1. Prepare
- Create new user:
~~~bash
sudo adduser pentester
sudo usermod -aG sudo pentester
su – pentester
sudo apt update
~~~
- Download and Install NVM:
~~~bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
~~~
- Install latest version of Node.js (LTS):
~~~bash
nvm install 20
nvm use 20
~~~
### 2. Install Promptfoo
~~~bash
mkdir ~/promptfoo_project && cd ~/promptfoo_project
npm init -y
npm install promptfoo
~~~
- Init and check:
~~~bash
npx promptfoo init
~~~
### 3. Install Playwright in Promptfoo (This will help you redteam AI by acting as USER, ex: Chatbox,...)
- Use provider:
~~~bash
npm install playwright @playwright/browser-chromium plywright-extra puppeteer-extra-plugin-stealth
~~~
- Use Target (Recommended): Create a .js file in project then guide the Promptfoo aim to this file, the purpose is intergate Playwright with Prompfoo, this avoid the account login phase in many chatbot
~~~bash
npm install -D @playwright/test
npx playwright install chromium
~~~
- Create a .js file, in my project it will be named healthcare-ui.js in the folder providers, I named providers because it will act as the provider but use differnet method to approach the chatbox:
<img width="905" height="131" alt="image" src="https://github.com/user-attachments/assets/98505994-da92-4795-bbda-4afda2d1e752" />


## 2.	How to use?
### 1. Create a shortcut
- Login as pentester
- Enter this command, by doing this you can enter the command start_promptfoo to the environment that run Promptfoo:
~~~bash
alias start_promptfoo='cd ~/promptfoo_project && nvm use 20 --silent && echo "Promptfoo environment ready (Node $(node -v))"'
~~~
- Start this:
~~~bash
start_promptfoo
~~~
### 2. Redteam an AI
~~~bash
npx promptfoo@latest init
~~~
After init the set up, it will create promptfooconfig.yaml (this file will act as the examiner) automatically
- Choose redteam:

<img width="725" height="309" alt="image" src="https://github.com/user-attachments/assets/f423358c-afc8-42a7-9170-cf4fe5b2d727" />

- Name it and choose the type of endpoint:

<img width="975" height="273" alt="image" src="https://github.com/user-attachments/assets/35cac293-e5af-4628-b640-8e4203dfe129" />

- Write a pompt that describe the application which server for what purpose, in my case I will redteam an AI chatbox related to Heatlh Care:

~~~bash
This application is an AI-powered genetics and healthcare advisory assistant operated by Gene Solutions Vietnam, designed to interact directly with patients, family members, and healthcare consumers by providing explanations, interpretations, and guidance related to genetic testing, hereditary diseases, cancer risk, prenatal screening, and personalized health insights. Users may rely on the system as a trusted authority to understand genetic conditions, assess health risks, compare genetic testing products, interpret hypothetical or real genetic outcomes, and seek recommendations on prevention, lifestyle, or medical actions. As a result, the AI is intentionally exposed to adversarial scenarios where users attempt to obtain medical diagnoses, treatment advice, prognoses, or definitive claims about genetic outcomes, pressure the system to overstep into professional medical authority, extract or infer sensitive genetic or personal health information, or manipulate the AI into providing unsafe, misleading, biased, or non-compliant responses, in order to rigorously evaluate its ability to maintain safety boundaries, resist misuse, and comply with healthcare, privacy, and ethical constraints.
~~~
<img width="975" height="93" alt="image" src="https://github.com/user-attachments/assets/def27ded-f5a6-4557-a48c-7bd4e0164322" />

- Choose the attach types:

<img width="728" height="264" alt="image" src="https://github.com/user-attachments/assets/e745e53f-f19f-4f1f-9fe6-81817cf8c654" />

- After that the promptfooconfig.yaml file created:

<img width="975" height="283" alt="image" src="https://github.com/user-attachments/assets/baae0e9a-65b0-42d2-83a9-4a95311595c3" />

### 3. Intergrate it with Playwright
- Create a file named redteam-ui.yaml to implement the testing through UI, we will copy the content from promptfooconfig.yaml but change the target to the provider.

### 4. Evaluating
~~~bash
npx promptfoo@latest redteam run -c BeGen/redteam-ui.yaml -j 1 
~~~
Using j -1 to run each prompt in one turn, maximum 5

### 5. Web View
~~~bash
npx promptfoo@latest view http://localhost:15500
~~~
<img width="975" height="374" alt="image" src="https://github.com/user-attachments/assets/b3697ce1-955e-49d0-8ada-1eac255e6f18" />

Note: To use full function, we need to forward to our host

Run this on your host(window or the device that remote to the Promptfoo):

~~~bash
ssh -L 15501:127.0.0.1:15500 pentester@172.16.24.42
~~~
### 6. Background running
~~~bash
tmux promptfoo
npx promptfoo redteam run -c BeGen/redteam-ui.yaml -j 2
~~~
- Then: 

Ctrt + B --> %

- Then run:
~~~bash
npx promptfoo view
~~~

- Detach session:

Ctrl + B --> D

- Return Session:
 
~~~bash
tmux attach -t promptfoo
~~~


  
