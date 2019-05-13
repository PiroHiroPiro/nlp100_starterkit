FROM python:3.7

ENV PYTHONUNBUFFERED=1

RUN apt-get update -y --fix-missing \
    && apt-get install -y --no-install-recommends \
    git \
    make \
    curl \
    xz-utils \
    file \
    patch \
    locales

ENV DEBIAN_FRONTEND noninteractive

RUN echo "ja_JP.UTF-8 UTF-8" > /etc/locale.gen \
    && locale-gen ja_JP.UTF-8 \
    && dpkg-reconfigure locales \
    && /usr/sbin/update-locale LANG=ja_JP.UTF-8

ENV LC_ALL=ja_JP.UTF-8
ENV LANG=ja_JP.UTF8

RUN cd /tmp \
    && curl -L -o mecab-0.996.tar.gz "https://drive.google.com/uc?export=download&id=0B4y35FiV1wh7cENtOXlicTFaRUE" \
    && tar zxfv mecab-0.996.tar.gz \
    && cd mecab-0.996 \
    && ./configure --with-charset=utf-8 \
    && make \
    && make install \
    && cd / \
    && rm /tmp/mecab-0.996.tar.gz \
    && rm -fr /tmp/mecab-0.996 \
    && ldconfig

RUN cd /tmp \
    && git clone --depth 1 https://github.com/neologd/mecab-ipadic-neologd.git \
    && cd mecab-ipadic-neologd \
    && ./bin/install-mecab-ipadic-neologd -n -u -y \
    && cd / \
    && rm -rf /tmp/mecab-ipadic-neologd \
    && ldconfig

COPY ./docker/python/etc/mecabrc /usr/local/etc/

WORKDIR /usr/src/source

ENV PIPENV_VENV_IN_PROJECT=1

COPY ./source/Pipfile  ./

RUN pip install --upgrade pip \
    && pip install pipenv \
    && pipenv install --skip-lock --system --dev

CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--allow-root", "--NotebookApp.token="]
