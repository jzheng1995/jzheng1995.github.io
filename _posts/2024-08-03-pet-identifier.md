---
title: "Building an image classifier"
---

Hi everyone. I'm going to review the steps in creating and deploying a basic pet breed identifier for cats and dogs.

The guide is mostly based off [fast.ai's chapter 2: Deployment](https://course.fast.ai/Lessons/lesson2.html) and [Dr. Tanishq's blog](https://www.tanishq.ai/blog/posts/2021-11-16-gradio-huggingface.html). Several changes had to be made due to Gradio's deprecation of several APIs.

The deployed app follows through these steps:
1. Training pet classifier model
2. Set up Hugging Faces space repository
3. Deploy

## Model training

Our model is based on a ResNet50 image classifier trained on Oxford Pets dataset.

```
from fastai.vision.all import *
path = untar_data(URLs.PETS)
dls = ImageDataLoaders.from_name_re(path, get_image_files(path/'images'), pat='(.+)_\d+.jpg', item_tfms=Resize(460), batch_tfms=aug_transforms(size=224, min_scale=0.75))
learn = vision_learner(dls, models.resnet50, metrics=accuracy)
learn.fine_tune(1)
learn.path = Path('.')
learn.export()
```

This code creates an export.pkl file. Files that end with .pkl are called pickle and represent preserved Python object. In our case, export.pkl represents our image classifier model.

## Hugging Faces space

The next step is to create a [Hugging Faces](https://huggingface.co/) account. Create a [space](https://huggingface.co/spaces). From my understanding, a hugging space space acts as a repository for your app files and automatically deploys (launches the app) for you. 

For those of you who aren't familiar with repositories, treat it like a file storage for your app file components. Once you place all your files in there, our deployment knows to look for files inside that repository to spin together your app. Another point to take note of is that not only do you have to define app files, you may also need to include the file dependencies (Python packages in this case) as well. This concept has to do with software development and reproducibility. To define these package dependencies, just simply include a requirements.txt file that names your package (and maybe version, I'll come back to this later). In my case, I had to add `fastai` and `scikit-image`.

To see the files in my pet identifier app, check [here](https://huggingface.co/spaces/jchzheng/catdog/tree/main). While it may be more efficient to [clone the repository](https://huggingface.co/docs/hub/en/repositories-getting-started) and work locally, I simply uploaded the files through the Hugging Face UI in my space repository (see below). Feel free to download the files in [my repository](https://huggingface.co/spaces/jchzheng/catdog/tree/main) and upload to your own.

![ Hugging Space upload](https://cdn-lfs.huggingface.co/datasets/huggingface/documentation-images/cec7a1aa3f1fb1c517f0e6f1f32e68a0428fb63c8d79af5a5382146929c18d28?response-content-disposition=inline%3B+filename*%3DUTF-8%27%27upload_files.png%3B+filename%3D%22upload_files.png%22%3B&response-content-type=image%2Fpng&Expires=1722993359&Policy=eyJTdGF0ZW1lbnQiOlt7IkNvbmRpdGlvbiI6eyJEYXRlTGVzc1RoYW4iOnsiQVdTOkVwb2NoVGltZSI6MTcyMjk5MzM1OX19LCJSZXNvdXJjZSI6Imh0dHBzOi8vY2RuLWxmcy5odWdnaW5nZmFjZS5jby9kYXRhc2V0cy9odWdnaW5nZmFjZS9kb2N1bWVudGF0aW9uLWltYWdlcy9jZWM3YTFhYTNmMWZiMWM1MTdmMGU2ZjFmMzJlNjhhMDQyOGZiNjNjOGQ3OWFmNWE1MzgyMTQ2OTI5YzE4ZDI4P3Jlc3BvbnNlLWNvbnRlbnQtZGlzcG9zaXRpb249KiZyZXNwb25zZS1jb250ZW50LXR5cGU9KiJ9XX0_&Signature=XTYvM-1uXY1cs6P-ixK9m%7Eiqi67617m8pI0MYOie-4NG2ur2V4iGB0iE0GKfjEDje8i%7Edr3GCSJoFaExMB2W%7EwvVDuVriMHSe9ZM2BqPlxVUbFddC9SdKbkkwU7EIHlDz08ScBjO3uWqEMGtFaZBC20WigaIq3SwFGXrkoVgnoJskpr8S5HTJXCW1YoR-VfpfH3cVFBKnCucotxCeW3alKf%7EiCtjhGOlHitOJzaq%7EvMvFLksclDCLvAaLjMIo9q2WhU5NVqdVb%7EauMeRQqBC2AeuCnqrEw-2-EniZMlqmPo6hbyLGrdC-CQbPoZ3bO2NCkUlTvxWSnOwaFQ%7Egmyn4Q__&Key-Pair-Id=K3ESJI6DHPFC7)

## Deployment

Gradio provides API interfaces for our app. Gradio allows us to conveniently set up ways for users to upload their own images and see a prediction output. Several API interfaces were deprecated in the 2024 version from the guides I used. Specifically, `gr.inputs.Image(shape=(512, 512)` had to be changed to `gr.Image(type = 'pil')`, `gr.outputs.Label(num_top_classes=3)` changed to `gr.Label(num_top_classes=3)`, and `interpretation`, `enable_queue` were deprecated and were not longer valid arguments. By the time you read this post Gradio may have changed its API again. You can likely fix deployment issues by using an older Gradio version (requirements.txt) or referring to [updated documentation](https://www.gradio.app/docs/gradio/interface). The space will automatically try to launch your app everytime you commit (save) your changes.

## Summary

This was an overview of how to deploy an image classifier using Hugging Face and Gradio. You can view [my demo here](/Pet_Identifier/). Feel free to contact me for questions and feedback!
