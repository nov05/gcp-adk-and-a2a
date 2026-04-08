# 🟢 Google Skills Lab - **Connect to Remote Agents with ADK and the Agent2Agent (A2A) SDK**   

* Lab - https://www.skills.google/paths/3273/course_templates/1275/labs/606600

<br><br>  

---   

## 👉 Task 1. Install ADK and set up your environment

```bash
git clone https://github.com/nov05/gcp-adk-and-a2a.git adk_and_a2a
export PATH=$PATH:"/home/${USER}/.local/bin"
python3 -m pip install --upgrade google-adk a2a-sdk google-genai
```

<br>   

## 👉 Task 2. Explore the ADK agent you plan to make available remotely

```bash
cd ~/adk_and_a2a
cat << EOF > illustration_agent/.env
GOOGLE_GENAI_USE_VERTEXAI=TRUE
# GOOGLE_CLOUD_PROJECT=qwiklabs-gcp-02-c909a799ef79
GOOGLE_CLOUD_LOCATION=global
MODEL=gemini-3-flash-preview
IMAGE_MODEL=gemini-3.1-flash-image-preview
EOF
```
```bash
cp illustration_agent/.env slide_content_agent/.env
```
```bash
adk web --allow_origins "regex:https://.*\.cloudshell\.dev"
```

<br>

## 👉 Task 3. Deploy the agent as an A2A Server

<br>

## 👉 Task 4. Enable another ADK agent to call this agent remotely

<br>

