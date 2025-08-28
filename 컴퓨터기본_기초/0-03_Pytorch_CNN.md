## Training 과정
* Dataset & DataLoader
```
img_transforms = transforms.Compose([
        transforms.RandomResizedCrop(224),
        transforms.RandomHorizontalFlip(0.5),
        transforms.RandomRotation( degrees = (-15, 15)),
        transforms.ToTensor(),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225],)
])

image_datasets = datasets.ImageFolader(os.path.join(data_root, 'train'), img_transforms )
img_dataloader = torch.utils.data.DataLoader(image_datasets, batch_size = 9, shuffle = True, num_works = 4)
class_names = image_dataloader.classes
```

* Training Loop

```
for epoch in range(num_epochs):
  for phase in ['train', 'vaild']:
    if phase == 'train':
      model.train()
    else:
      model.eval()

    running_loss = 0.0
    running_corrects = 0

    for inputs, labels in tqdm(dataloaders[phase]):
        inputs = inputs.to(device)
        labels = labels.to(device)

        optimizer.zero_grad()
```
```
for epoch in range(num_epochs):
  for inputs, labels in tqdm(dataloaders[phase]):
    with torch.set_grad_enabled(phase == 'train'):
        outputs = model(inputs)
        _, preds = torch.max(outputs, 1)
        loss = criterion(outputs, labels)
        if phase == 'train':
          loss.backward()
          optimizer.step()

    running_loss += loss.item() * inputs.size(0)
    running_corrects += torch.sum(preds == labels.data)

  if scheduler is not None and phase == 'train':
      scheduler.step()

  epoch_loss = running_loss / dataset_sizes[phase]
  epoch_acc = running_corrects.double() / dataset_sized[phase]
```
```
for epoch in range(num_epochs):
  if phase == 'train':
    train_loss.append(epoch_loss)
    train_acc.append(epoch_acc.item())
  else:
    valid_loss.append(epoch_loss)
    valid_acc.append(epoch_acc.item())
    if epoch_acc > best_acc:
      best_acc = epoch_acc

      if not os.path.exists(model_dir):
        os.makedirs(model_dir)

      model_save_path = os.path.join(model_dir, f'{model_name}.pth')
      torch.save(model.state_dict(), model_save_path)
```

## Image Data
* 이미지는 픽셀로 구성 - 픽셀은 picture element
* 각 픽셀은 이미지의 색상 정보를 담고 있음
  * Color Image: 각 채널별로 0 ~ 255 사이의 값을 가짐. Ex) RGB 3채널 이미지
  * Grayscale Image: 1채널 이미지로 0 ~ 255 사이의 값을 가

## Convolutional Neural Network
* 인간의 시신경의 특성을 반영하여 만든 딥러닝 구조
* 이전의 Linear layer(Fully connected layer)의 모델과 달리 공간 정보도 반영하고, 연산량을 줄임

## Convolution
* 입력 영상에 sliding-window 방식으로 Filter와 dot-product 연산 수행

## Pooling
* 입력 영상을 Down-sampling

## Alexnet
* 8층으로 구성된 모델, 5개의 Convolution Layer 와 3개의 Fully-Connected Layer로 구성

## VGGNet
* 보편적인 CNN모델, 3 x 3 필터를 모든 Conv Layer에 사용

## Efficientnet
* 네트워크의 Depth, Width, Resolution을 효과적으로 Scaling하여 정확성과 효율성을 높인 모델








































