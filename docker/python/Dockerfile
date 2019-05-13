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
    locales \
    fonts-ipafont

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

RUN cd /tmp \
    && curl -L -o CRF++-0.58.tar.gz 'https://drive.google.com/uc?export=download&id=0B4y35FiV1wh7QVR6VXJ5dWExSTQ' \
    && tar zxf CRF++-0.58.tar.gz \
    && cd CRF++-0.58 \
    && ./configure \
    && make \
    && make install \
    && cd / \
    && rm /tmp/CRF++-0.58.tar.gz \
    && rm -rf /tmp/CRF++-0.58 \
    && ldconfig

RUN cd /tmp \
    && DOWNLOAD_URL="https://drive.google.com`curl -c cookies.txt \
        'https://drive.google.com/uc?export=download&id=0B4y35FiV1wh7SDd1Q1dUQkZQaUU' \
        | sed -r 's/\"/\n/g' | grep id=0B4y35FiV1wh7SDd1Q1dUQkZQaUU | grep confirm | sed 's/&amp;/\&/g'`" \
    && curl -L -b cookies.txt -o /tmp/cabocha-0.69.tar.bz2 "$DOWNLOAD_URL" \
    && tar jxf cabocha-0.69.tar.bz2 \
    && cd cabocha-0.69 \
    && export CPPFLAGS=-I/usr/local/include \
    && ./configure --with-mecab-config=`which mecab-config` --with-charset=utf8 \
    && make \
    && make install \
    && cd python \
    && python setup.py build \
    && python setup.py install \
    && cd / \
    && rm /tmp/cabocha-0.69.tar.bz2 \
    && rm -rf /tmp/cabocha-0.69 \
    && rm /tmp/cookies.txt \
    && ldconfig

COPY ./docker/python/etc/mecabrc /usr/local/etc/
COPY ./docker/python/etc/cabocharc /usr/local/etc/

RUN apt-get install -y --no-install-recommends graphviz

WORKDIR /usr/src/source

ENV PIPENV_VENV_IN_PROJECT=1

COPY ./source/Pipfile  ./

RUN pip install --upgrade pip \
    && pip install pipenv \
    && pipenv install --skip-lock --system --dev

CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--allow-root", "--NotebookApp.token="]
