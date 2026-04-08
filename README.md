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
GOOGLE_CLOUD_PROJECT=$GOOGLE_CLOUD_PROJECT
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

To view the web interface in a new tab, click the http://127.0.0.1:8000 link at the bottom of the terminal output.   
A new browser tab opens with the ADK Dev UI.   
In the ADK Dev UI tab, from the Select an agent drop-down on the left, select the illustration_agent.   
Prompt the agent with some text that could be used in a recruitment slide deck:   
```text
By supporting each other, we get big things done!
```

<br>

## 👉 Task 3. Deploy the agent as an A2A Server

```bash
export PROJECT_ID=$GOOGLE_CLOUD_PROJECT
echo $PROJECT_ID

yes | gcloud services enable orgpolicy.googleapis.com --project=$PROJECT_ID --quiet
export REGION=$(gcloud org-policies describe constraints/gcp.resourceLocations \
  --project=$PROJECT_ID \
  --format="value(spec.rules[0].values.allowedValues)" \
  | grep -oP '(?<=in:)(us|europe|asia)[a-z0-9-]+(?=-locations)' \
  | head -n 1)
echo $REGION

export ZONE=$(gcloud compute zones list \
  --filter="region:($REGION)" \
  --format="value(name)" \
  | head -n1)
echo $ZONE

export PROJECT_NUMBER=$(gcloud projects describe $GOOGLE_CLOUD_PROJECT --format="value(projectNumber)")
echo $PROJECT_NUMBER

export GOOGLE_CLOUD_LOCATION=global
echo $GOOGLE_CLOUD_LOCATION
```
```bash
cd ~/adk_and_a2a
envsubst < illustration_agent/agent_template.json > illustration_agent/agent.json
cat illustration_agent/agent.json
```
```bash
adk deploy cloud_run \
    --project $PROJECT_ID \
    --region $REGION \
    --service_name illustration-agent \
    --a2a \
    illustration_agent
```
```bash
gcloud run services update illustration-agent \
  --project=$PROJECT_ID \
  --region=$REGION \
  --set-env-vars="GOOGLE_CLOUD_LOCATION=$GOOGLE_CLOUD_LOCATION"
```

<br>

## 👉 Task 4. Enable another ADK agent to call this agent remotely

```bash
cd ~/adk_and_a2a
cp illustration_agent/agent.json illustration-agent-card.json
cat illustration-agent-card.json
```
```bash
cd ~/adk_and_a2a
adk web --allow_origins "regex:https://.*\.cloudshell\.dev"
```

Once again, click the http://127.0.0.1:8000 link in the terminal output to launch the ADK Dev UI.   
In the ADK Dev UI browser tab, from the Select an agent drop-down on the left, select the `slide_content_agent`.    
Prompt the agent with an idea for a slide as follows:     
```text  
Create content for a slide about our excellent on-the-job training.
```

<br>

