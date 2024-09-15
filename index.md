---
layout: splash-home
hidden: true
header:
  overlay_color: "#5e616c"
  overlay_image: /assets/img/background/digitalart.png
  actions:
    - label: "<i class='fa fa-book' aria-hidden='true'></i>  Read posts"
      url: "/posts/"
excerpt: >
  Making sense of data
feature_row:
  - image_path: /assets/img/headshot.jpeg
    alt: "about"
    title: "Welcome to my website!"
    excerpt: "My name is Jason Zheng and this is where I share my projects, analyses, and ideas. Enjoy!"
    url: "/about/"
    btn_class: "btn--primary"
    btn_label: "Learn more"
    feature_row:
  - image_path: /assets/img/misc/dashboard.jpeg
    alt: "dashboard"
    title: "Labour Market Dashboard"
    excerpt: "Analytic dashboard made using StatCan Labour data and Streamlit."
    url: "/Labour-Dashboard/"
    btn_class: "btn--primary"
    btn_label: "See Demo"
  - image_path: /assets/img/misc/vertex.jpeg
    alt: "google-cloud-model"
    title: "Deploying ML on cloud"
    excerpt: "Deploying a classification model on Google Cloud."
    url: "/2024/09/14/vertex-deployment/"
    btn_class: "btn--primary"
    btn_label: "Learn more"
feature_row2:
  - image_path: /assets/img/misc/forecast_mini.jpeg
    alt: "forecast"
    title: Timeseries Forecast
    excerpt: "Forecasting London energy consumption using XGBoost."
    url: "/forecasting/01-energyforecast"
    btn_class: "btn--primary"
    btn_label: "Learn more"
  - image_path: /assets/img/misc/churn.jpg
    alt: "churn"
    title: Bank Churn
    excerpt: "Predicting bank churn using Python and Scikit-learn."
    url: "https://github.com/jzheng1995/Bank-Churn"
    btn_class: "btn--primary"
    btn_label: "Learn more"
  - image_path: /assets/img/misc/handshake.jpg
    alt: "cross-sell"
    title: Insurance Cross Sell
    excerpt: "Predicting Insurance Cross Sell with Bayesian Optimization."
    url: "https://github.com/jzheng1995/Insurance-Cross-Sell"
    btn_class: "btn--primary"
    btn_label: "Learn more"
feature_row3:
  - image_path: /assets/img/misc/pets.jpeg
    alt: "pets"
    title: "Pet Identifier"
    excerpt: "Breed identifier for pets! Deployed with Hugging Face and Gradio."
    url: "/Pet_Identifier/"
    btn_class: "btn--primary"
    btn_label: "See Demo"
  - image_path: /assets/img/misc/homecredit.jpeg
    alt: "home-credit"
    title: Home Credit
    excerpt: "SQL extraction and machine learning in R"
    url: "https://github.com/jzheng1995/Insurance-Cross-Sell"
    btn_class: "btn--primary"
    btn_label: "Learn more"
---

{% include feature_row %}
{% include feature_row id="feature_row2"  %}
{% include feature_row id="feature_row3"  %}