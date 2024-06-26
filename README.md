## Description:

NetworkWrapper is a convenience class for working with neural networks using PyTorch. 
With it you can:
* Train and retrain models
* Display metrics (including class-specific ones)
* Make beautiful visualizations of models in the form of histograms and graphs
* Predict the probabilities of object labels or just labels. 5. Save the state of the model and optimizer by epoch
* Save the best weights, with saving the best weights.

And everything is accompanied by beautiful and formatted output, working in 3 lines of code!

The class contains 650 lines of code, and it took almost a month to write. Everything - from paths, separators, depending on the type of OS, type of device for training, and right down to the display formats of progress bars, figsizes (without processing in PyCharm, the pbar of Jupiter/colab moves out, but in Jupiter/colab the pbar PyCharm is moving out) are done AUTOMATICALLY :)

And this class can do a lot more - including retraining a model from some epoch :) 
You started training model for 20,50 or even 100 epochs and went for a walk/mind my own business - 
and then came and looked at all the statistics and loaded into the desired epoch (if you want). 
Or, using 1 line of code, 
You trimmed the saved model and optimizer weights for all epochs, starting with the desired one. 
Or even threw out all the era weights except the best one. 
And there is no need to restart training many times, 
fearing that the model will be overtrained or undertrained for the entered number of epochs. Just a fairy tale)

The best way to support my creation is to star the project on Github :)

GitHub: https://github.com/JohnConnor123/NetworkWrapper
PyPI: https://pypi.org/project/nn-wrapper/  
Contact: ivan.eudokimoff2014@gmail.com

P.s. There may be minor bugs (and I tried very hard to avoid them and spent more than a week debugging the code). If you have a bug, open an issue on the project's Github or just write to me by email.

## Quick usage guide
P.s. Contains only the basic possibilities of the class

### Installing
First install the package using pip:
```python
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
pip3 install nn-wrapper
```
By default, without first command the library will install the non-CUDA version of torch.

### Importing
P.s. Optional: we set the main parts of the path of paths in windows and colab, relative to which relative paths are specified.

```python
from NetworkWrapper import NetworkWrapper

main_windows_path = "D:\\Python_Projects\\Jupyter\\DL MIPT Stepik\\"
main_colab_path = r'/content/gdrive/MyDrive/Colab Notebooks/Deep Learning School/'
NetworkWrapper.set_main_paths(main_windows_path, main_colab_path)
```

### Creating NetworkWrapper object
We create a NetworkWrapper object, wrapping any neural network model in it and passing all the parameters.
```python
model_testing = NetworkWrapper(model=model, epochs=5, batch_size=32, num_workers=0,
                               train_dataset=train_dataset, val_dataset=val_dataset,
                               n_classes=n_classes, colab_view=False,
                               relative_path='Models\\Transfer learning\\efficientnet_b1.pth',
                               lr=1e-3, scheduler_gamma=0.9,
                               load_pretrained_model=True)
```
P.s. The optimizer, scheduler and criterion are not passed to the initializer - baselines from classification tasks are used:
```python
self.optimizer = torch.optim.Adam(model.parameters(), lr=lr)
self.scheduler = ExponentialLR(self.optimizer, gamma=scheduler_gamma)
self.criterion = nn.CrossEntropyLoss()
```
But you can change objects by explicitly specifying them after initialization:
```python
model_testing.optimizer = ...
model_testing.scheduler = ...
model_testing.criterion = ...
```

### Initializing the model
We start initializing the model. 
The model is trained or loaded if a trained model is found. 
Then, by default, the main metrics are calculated - 
this can be controlled using the "calculate_metrics" parameter 
of the "train_load_model" method.
```python
model_testing.train_load_model()
```
### Printing and displaying all information after model initialization
```python
print(f"Best epoch: {model_testing.best_epoch}    "
      f"Loaded epoch: {model_testing.loaded_epoch}    "
      f"Total epochs count: {model_testing.total_epochs}")
print("Unpredicted classes", set(model_testing.actual_labels) - set(model_testing.y_preds))
print(model_testing.get_metrics())
print(model_testing.get_metrics('stats_by_class'))
```
![image](https://github.com/JohnConnor123/NetworkWrapper/assets/106041597/89cf18aa-0812-4645-91b4-dd37d17ae777)


```python
model_testing.plot_metrics()
```
![image](https://github.com/JohnConnor123/NetworkWrapper/assets/106041597/fbe543e5-9b22-485a-a0ca-6bd4b754e324)


```python
model_testing.plot_correct_class_prediction_hist()
```
![image](https://github.com/JohnConnor123/NetworkWrapper/assets/106041597/80aaa57a-8e5d-4872-b379-4ac4d44a3ae0)


```python
model_testing.plot_confidence_on_examples()
```
![image](https://github.com/JohnConnor123/NetworkWrapper/assets/106041597/77387065-2a95-4d1a-af13-a9c1dcf270f8)


### Loading a specific epoch
```python
model_testing.load_epoch(epoch_to_load)
```


### Trimming the save file
Removing model weights and optimizer weights after "last_untruncated_epoch" epoch:
```python
model_testing.truncate_dump_file(last_untruncated_epoch=last_untruncated_epoch)
```


### Renewal learning from a specific epoch
```python
model_testing.resume_model_training(start_epoch=start_epoch, total_epochs=6,
                                    relative_path='Transfer learning\\resumed_trained.pth')
```


### Removing all epochs from the dump file except the best epoch
```python
model_testing.drop_all_epochs_from_dump_file_except_best_epoch()
```

### Viewing and changing system settings
Additional feature: you can view and change most of the protected attributes 
that are responsible for various wrapper settings. 
And all this is done simply through a dot, without cluttering the namespace!

For example, you can change pyplot figsize:
```python
model_testing.protected_attributes.figsize = (6, 4)
```

### Example code to demonstrate the operation of the main functionality of the library:
```python
from nn-wrapper import NetworkWrapper
main_windows_path = "D:\\Python_Projects\\Jupyter\\DL MIPT Stepik\\"
main_colab_path = r'/content/gdrive/MyDrive/Colab Notebooks/Deep Learning School/'
NetworkWrapper.set_main_paths(main_windows_path, main_colab_path)

model = models.efficientnet_b1(pretrained=True)
model.classifier[1] = nn.Linear(in_features=1280, out_features=n_classes)

model_testing = NetworkWrapper(model=model, epochs=5, batch_size=32, num_workers=0,
                               train_dataset=train_dataset, val_dataset=val_dataset,
                               n_classes=n_classes, colab_view=False,
                               relative_path='Models\\Transfer learning\\efficientnet_b1.pth',
                               lr=1e-3, scheduler_gamma=0.9,
                               load_pretrained_model=True)

model_testing.train_load_model()

print(f"Best epoch: {model_testing.best_epoch}    "
      f"Loaded epoch: {model_testing.loaded_epoch}    "
      f"Total epochs count: {model_testing.total_epochs}")
print("Unpredicted classes", set(model_testing.actual_labels) - set(model_testing.y_preds))
print(model_testing.get_metrics())
print(model_testing.get_metrics('stats_by_class'))
model_testing.plot_metrics()
model_testing.plot_correct_class_prediction_hist()
model_testing.plot_confidence_on_examples()

epoch_to_load = model_testing.total_epochs//2
print(f"\n\nLoading epoch #{epoch_to_load}")
model_testing.load_epoch(epoch_to_load)
print(f"Best epoch: {model_testing.best_epoch}    "
      f"Loaded epoch: {model_testing.loaded_epoch}    "
      f"Total epochs count: {model_testing.total_epochs}")
print(model_testing.get_metrics())
print(model_testing.get_metrics('stats_by_class'))
print("Unpredicted classes", set(model_testing.actual_labels) - set(model_testing.y_preds))
model_testing.plot_metrics()
model_testing.plot_correct_class_prediction_hist()
model_testing.plot_confidence_on_examples()

print("\nLoading best epoch")
model_testing.load_epoch(model_testing.best_epoch)
print(f"Best epoch: {model_testing.best_epoch}    "
      f"Loaded epoch: {model_testing.loaded_epoch}    "
      f"Total epochs count: {model_testing.total_epochs}")
print(model_testing.get_metrics())
print(model_testing.get_metrics('stats_by_class'))
print("Unpredicted classes", set(model_testing.actual_labels) - set(model_testing.y_preds))
model_testing.plot_metrics()
model_testing.plot_correct_class_prediction_hist()
model_testing.plot_confidence_on_examples()

last_untruncated_epoch = 3  # example
print(f"\nTruncate dump file. Last_untruncated_epoch: {last_untruncated_epoch}")
model_testing.truncate_dump_file(last_untruncated_epoch=last_untruncated_epoch)
print(f"Best epoch: {model_testing.best_epoch}    "
      f"Loaded epoch: {model_testing.loaded_epoch}    "
      f"Total epochs count: {model_testing.total_epochs}")
print(model_testing.get_metrics())
print(model_testing.get_metrics('stats_by_class'))
print("Unpredicted classes", set(model_testing.actual_labels) - set(model_testing.y_preds))
model_testing.plot_metrics()
model_testing.plot_correct_class_prediction_hist()
model_testing.plot_confidence_on_examples()

start_epoch = 2
model_testing.resume_model_training(start_epoch=start_epoch, total_epochs=6,
                                    relative_path='Transfer learning\\resumed_trained.pth')
print(f"Best epoch: {model_testing.best_epoch}    "
      f"Loaded epoch: {model_testing.loaded_epoch}    "
      f"Total epochs count: {model_testing.total_epochs}")
print("Unpredicted classes", set(model_testing.actual_labels) - set(model_testing.y_preds))
print(model_testing.get_metrics())
print(model_testing.get_metrics('stats_by_class'))
model_testing.plot_metrics()
model_testing.plot_correct_class_prediction_hist()
model_testing.plot_confidence_on_examples()

print("\nDrop all epochs from dump file except best epoch")
model_testing.drop_all_epochs_from_dump_file_except_best_epoch()
print(model_testing.get_metrics())
print(model_testing.get_metrics('stats_by_class'))
print("Unpredicted classes", set(model_testing.actual_labels) - set(model_testing.y_preds))
model_testing.plot_metrics()
model_testing.plot_correct_class_prediction_hist()
model_testing.plot_confidence_on_examples()
```
