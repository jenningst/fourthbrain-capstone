# Samsung OCT Group #1
    1. Asieh Harati
    2. Troy Jennings
    3. Wolfgang Black

## Data Lineage
- Mendeley Data (https://data.mendeley.com/datasets/rscbjbr9sj/2)

## Usage
### Adding the SavedModel
Since Github has restrictions on large files, the repo doesn't include the saved model. To have this demo run correctly (i.e. build and run the API) you need to first:
1. Copy all the files included in the saved model (finetune_20epochs_20k_unlabeled_1k_labeled) into `/models/saved_models/simclr_net`
2. Copy a few sample files into the `/corpus` directory so you can test the `corpus_predict` endpoing

### Building
- To build a docker image from the project run the following command from the root project directory run:

```
docker build -t <TAG_NAME> .
```

### Running the API Locally
This project uses FastAPI to expose a handful of API endpoints for:
- `GET /` -- A basic API "liveness" test
- `GET /predict{image}` -- Making a prediction given a sample image
- `GET /corpus_predict`Making a prediction from a random sample in an included corpus of images

To start the API in hot-reload mode, run the following command from the `/src` directory:

```
uvicorn classifier.inference:api --reload
```

After the API service starts, open a browser to http://localhost:8000 to test the above endpoints.


## Questions     
1. Perform feature engineering 
    - For Neural Nets a large amount of traditional feature engineering is largely unnecessary. The benefit of the myriad of non-linear connections allow the NN to learn 'features' and deprioritize the less useful features. It's one of the strengths of NN, but typically is the reason why larger datasets are necessary. For our particular use case, if you squint, you could consider the image augmentation used in the simclr model to be like feature engineering. This augmentation is found in [`get_augmenter`](https://github.com/jenningst/fourthbrain-capstone/blob/main/simclr_test/simclr%20model%20V2.ipynb) (cell 6) and can be seen to affect image crop, view, brightness, and color.

2. Retrain models and tune hyperparameter 
    - For our project prompt, we have a model reported in a paper () trained on 1000 images with an accuracy of 93%. This benchmark is compared to 2 models developed by our team, a transfer learning inceptionV3 also trained on 1000 images and a student net created via the simclr method trained on 20k unlabeled images and the same 1000 labeled images.
    - In these methods finetuning takes a specific form:
        - For transfer learning, finetuning occurs in the final FC layer with frozen weights, our limited model achieves a validation accuracy of ~90% after 10 epochs.
        - For our student net, finetuning occurs after the teacher net encoder, which utilizes a base resnet50 architecture, again at the output layer. This model achieves a maximum validation accuracy of 96.5% in 20 epochs and a maximum validation accuracy of 100% after 100 epochs.

3. Leverage AutoML to benchmark your model
    - Since we're already comparing multiple models we've decided not to explore AutoML Vision, but we'd expect the results to be similar to the transfer learning.

4. Document performance, interpretation, and learnings in markdown
    - The performance metrics using training and validation sets are noted above in section 2. Given that we utilize balanced classes in each of the models, explainability of the results using accuracy works well in this context. From the observed results, the models perform well, even without utilizing all available data which was an interesting outcome for the project. We intend to measure sensitivity and specificity in future model iterations to align with the publication results discussion.
