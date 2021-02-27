# demo-doc-type-react-210227

## 0. Motivation

* DockerでReact&TypeScriptの環境を構築したい
* ESlintとPrettierの設定も入れられるといいな

## 1. ゼロから始める環境構築

### ~ docker-compose build

```
$ mkdir demo-doc-type-react-210227
$ cd demo-doc-type-react-210227
$ vim Dockerfile          // Dockerfileを記述
$ vim docker-compose.yml  // docker-compose.ymlを記述
$ docker-compose build
```

### create-react-app

```
$ docker-compose run --rm frontend sh -c 'npx create-react-app frontend --template typescript'
```

### アプリ起動（Control + Cで終了）

```
$ docker-compose up
```

## 2. GitHubからcloneする環境構築

### git clone

```
$ git clone https://github.com/keisukesaito7/demo-doc-type-react-210227.git
```

### 起動まで
```
$ docker-compose build
$ docker-compose run --rm frontend sh -c 'cd frontend && yarn install'
$ docker-compose up
```
