# Copyright 2017 Neural Networks and Deep Learning lab, MIPT
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM python:3.7-slim-stretch

ARG COMMIT=master

EXPOSE 5000

WORKDIR /base

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        build-essential \
        git \
        locales && \
    printf '%s\n%s\n' 'en_US.UTF-8 UTF-8' 'ru_RU.UTF-8 UTF-8' >> /etc/locale.gen && \
    locale-gen && \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false && \
	rm -rf /var/lib/apt/lists/* && \
	pip install virtualenv==16.7.5 && \
	virtualenv venv && \
	mkdir -p /venv/dp_internal_token_dir && \
    rm -rf /root/.cache

ENV PATH=/base/venv/bin:$PATH
ENV LANG='en_US.UTF-8' LANGUAGE='en_US.UTF-8' LC_ALL='en_US.UTF-8'

RUN git clone https://github.com/deepmipt/DeepPavlov.git && \
    cd DeepPavlov && \
    git checkout $COMMIT && \
    python setup.py develop && \
    python -c 'import deeppavlov.models' && \
    rm -rf /root/.cache

RUN pip install email-validator
RUN pip install --upgrade pip

RUN mkdir /base/config
RUN mkdir /base/train
RUN mkdir -p /base/deep/downloads/embeddings

COPY config/intents_sample_json_run.json /base/config/intents_sample_json_run.json
COPY config/intents_sample_json_train.json /base/config/intents_sample_json_train.json
#ADD http://files.deeppavlov.ai/deeppavlov_data/embeddings/dstc2_fastText_model.bin /base/deep/downloads/embeddings/dstc2_fastText_model.bin
COPY config/dstc2_fastText_model.bin /base/deep/downloads/embeddings/dstc2_fastText_model.bin

COPY train/train.json /base/train/train.json

RUN mkdir -p /root/.deeppavlov

RUN python -m deeppavlov install /base/config/intents_sample_json_train.json
RUN python -m deeppavlov download /base/config/intents_sample_json_train.json
RUN python -m deeppavlov train /base/config/intents_sample_json_train.json

CMD python -m deeppavlov riseapi /base/config/intents_sample_json_run.json