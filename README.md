# 🎈 Streamlit + LLM Examples App

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/streamlit/llm-examples?quickstart=1)

Starter examples for building LLM apps with Streamlit.

## Overview of the App

This app showcases a growing collection of LLM minimum working examples.

Current examples include:

- Chatbot
- File Q&A
- Chat with Internet search
- LangChain Quickstart
- LangChain PromptTemplate
- Chat with user feedback

## Demo App

[![Streamlit App](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://llm-examples.streamlit.app/)

### Get an OpenAI API key

You can get your own OpenAI API key by following the following instructions:

1. Go to https://platform.openai.com/account/api-keys.
2. Click on the `+ Create new secret key` button.
3. Next, enter an identifier name (optional) and click on the `Create secret key` button.

### Enter the OpenAI API key in Streamlit Community Cloud

To set the OpenAI API key as an environment variable in Streamlit apps, do the following:

1. At the lower right corner, click on `< Manage app` then click on the vertical "..." followed by clicking on `Settings`.
2. This brings the **App settings**, next click on the `Secrets` tab and paste the API key into the text box as follows:

```sh
OPENAI_API_KEY='xxxxxxxxxx'
```

## Deploy to GCP cloud run with API stored securely


> Build image from dockerfile

```ps
docker build -t chatbot .
```

> Log in to GCP

```ps
gcloud init
```

> Ensure that the Cloud Run API, Container Registry API and Secretmanager API are enabled:

```ps
gcloud services enable run.googleapis.com
gcloud services enable containerregistry.googleapis.com
gcloud services enable secretmanager.googleapis.com
```

> create the secret slot in gcp

```ps
gcloud secrets create openai_api_key --replication-policy="automatic" --project=[PROJECT-ID]
```

> add the key itself

```ps
echo -n "[API-KEY]" | gcloud secrets versions add openai_api_key --data-file=- --project=[PROJECT-ID]
```

> Grant the necessary role to the service account to allow it to access secrets in Secret Manager. The role required is roles/secretmanager.secretAccessor.

```ps
gcloud projects add-iam-policy-binding [PROJECT-ID] --member="serviceAccount:[SERVICE-ACCOUNT]" --role="roles/secretmanager.secretAccessor"
```

> Tag Your Image: Replace [PROJECT-ID] with your GCP project ID.

```bash
docker tag chatbot gcr.io/[PROJECT-ID]/chatbot-clean
```

> Push the Image to GCR:

```bash
gcloud auth configure-docker
docker push gcr.io/[PROJECT-ID]/chatbot-clean
```

Now deploy app to Cloud Run:

```bash
gcloud run deploy chatbot-service-clean --image gcr.io/[PROJECT-ID]/chatbot-clean --platform managed --region europe-west2 --allow-unauthenticated
```