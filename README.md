Simple intent detection service based on DeepPavlov.ai.

**This should be used only if you have large amount of training data**

Training is happening on image build event. Configuration should be similar to https://doc.livehelperchat.com/docs/bot/deeppavlov-faq/ except response location should be `0:0` instead of `0:0:0`

* You should edit `Dockerfiles/deep/train/train.json` with your data before building an image.
* See `Dockerfiles/deep/Dockerfile` for getting `dstc2_fastText_model.bin` file 

```shell
docker-compose -f docker-compose.yml build
docker-compose -f docker-compose.yml up
```

Run as service once it's build

```shell
docker-compose -f docker-compose.yml up -d
```

Testing

```
curl -X POST "http://localhost:5000/model" -H "accept: application/json" -H "Content-Type: application/json" -d "{\"x\":[\"I want to book place for two\"]}"
```

Response if you did not change train data

```json
[["BookRestaurant",[0.2782818078994751,0.22770161926746368,0.30094414949417114,0.6002139449119568,0.2533802390098572,0.25423356890678406,0.21291431784629822],3]]
```