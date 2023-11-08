---
title: Frequently Asked Questions
sidebar_label: FAQ
---

## How does FoloToy Server work?

There are three key steps:

- **Recording** Receive real-time recording data sent by the toy through UDP, and call the STT (Sound-To-Text) API to convert the sound into text. Currently supported STT options include: `openai-whisper`, `azure-stt`, `azure-whisper`.
- **Thinking**: After receiving the previous text, the LLM (Large-Language-Model) API will be immediately called to obtain sentences generated by the LLM in a streaming manner. Then, the TTS (Text-To-Sound) API is called to convert the sentences into human speech. Currently supported LLM options include: `openai`, `azure-openai` Or llm proxied by [One-Api](https://github.com/songquanpeng/one-api).
- **Play audio**: Toys will receive TTS (Text-To-Sound) audio file streams generated by the FotoLoy Server and play them according to the order. Currently supported TTS options include: `openai-tts`, `azure-tts`, `elevenlabs`, `edge-tss`(Free).

## Can't connect to your own server?

Please check if the following ports are open. If the hosting provider has a firewall or access rules set up, these ports also need to be opened.

In the default configuration of folotoy-server-self-hosting docker-compose.yml, the following ports are used: 1883/tcp, 8082/tcp, 18083/tcp, 8083/tcp, and 8085/udp:

* 1883/tcp: **required**, Toy connects to the server via MQTT (1883/tcp)
* 8082/tcp: **required**, Toy downloads startup voice streams and conversation voice streams through this HTTP service port
* 8085/udp: **required**, Toy uploads real-time voice streams through this UDP port
* 18083/tcp: **optional**, HTTP port for EMQX's web management console
* 8083/tcp: **optional**, Websocket port for EMQX

## Can't see the serial port device after connecting the flashing USB cable?

**Please make sure that the toy is powered on. Flashing needs to be done in a powered-on state. It is not possible to connect only the circuit board with a USB cable.**

* Flashing tool address: [tool.folotoy.com](https://tool.folotoy.com)
* Please use the latest version of Chrome or Edge browser to access the flashing tool for flashing.
* Please confirm if the flashing cable is the data cable provided with the toy. Some USB cables only have charging function and do not have data function, so you need to use a USB cable with data function.
* If it is a Windows system and cannot find the serial port device after inserting the USB cable, please try installing drivers.
* MacOS does not require driver installation. The baud rate for flashing on MacOS can be set as 460800 or lower.

## How to make the changes in `roles.json` take effect?

Execute the following command in the folotoy-server-self-hosting directory to restart the service.

Note: After restarting the service, the toy also needs to be turned off and on again or press any role switch key once to reconnect to the server.

```bash
sudo docker compose folotoy restart
```

## Can't find the model when using azure openai?

The model name of azure-openai is different from that of openai. Azure-openai uses deployment names, for example, when deploying gpt-3.5-turbo, you can set the deployment name as gpt-35. Therefore, in roles.json, you must use gpt-35.

```json
{
  "2": {
    "model": "gpt-35",
    "start_text": "Hello, I am Dongbei Rabbit. How can I help you?",
    "prompt": "You are a knowledgeable and helpful intelligent robot named 'Dongbei Rabbit'. Your task is to chat with me. Please use short dialogues and speak in Chinese within 50 characters each time!",
    "max_message_count": 20,
    "temperature": 0.7,
    "max_tokens": 800,
    "top_p": 0.95,
    "frequency_penalty": 0,
    "presence_penalty": 0,
    "voice_name":"zh-CN-liaoning-XiaobeiNeural",
    	"language":"zh",
    	"stt_type":"openai-whisper"
   }
}
```


## Already configured the key and base_uri correctly, but openai-whisper is reporting an error?

The language of openai-whisper can be left unset, but if it is set, the correct language code must be used. The language codes are different from azure-stt. For example, for Chinese in openai-whisper, use `zh`. Configure the correct language code for each role in the roles.json file. Click here to view all language codes: [OpenAI Whisper language 639-1 codes](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes)

```json
{
  "2": {
    "model": "gpt-3.5-turbo",
    "start_text": "Hello, I am Dongbei Rabbit. How can I help you?",
    "prompt": "You are a knowledgeable and helpful intelligent robot named 'Dongbei Rabbit'. Your task is to chat with me using short conversations in Chinese that do not exceed 50 characters per response!",
    "max_message_count": 20,
    "temperature": 0.7,
    "max_tokens": 800,
    "top_p": 0.95,
    "frequency_penalty": 0,
    "presence_penalty": 0,
    "voice_name":"zh-CN-liaoning-XiaobeiNeural",
    	"language":"zh",
    	"stt_type":"openai-whisper"
   }
}
```
If using the docker image version `v23.45.1.1` or above, you can also configure it like this:

```json
{
  "2": {
    "start_text": "Hello, I am Dongbei Rabbit. How can I help you?",
    "prompt": "You are a knowledgeable and helpful intelligent robot named 'Dongbei Rabbit'. Your task is to chat with me. Please use short conversations and speak in Chinese within 50 characters each time!",
    "max_message_count": 20,
    "stt_type": "openai-whisper",
    "stt_config: {
        "language": "zh"
    },
    "llm_config": {
        "model": gpt-3.5-turbo",
        temperature: 0.7,
        max_tokens: 800,
        top_p: 0.95,
        frequency_penalty: 0,
        presence_penalty: 0
    },
     tts_config ": {
         voice_name ": zh-CN-liaoning-XiaobeiNeural "
     }
   }
}
```

## Has the key and region been configured correctly, but azure-stt is reporting an error?

azure-stt needs to use the correct language code. The language code for azure-stt is different from openai-whisper. For example, Chinese in azure-stt should use `zh-CN`. Configure the correct language code for each role in the roles.json file. Click here to view all language codes: [Azure STT language BCP-47 codes](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/language-support?tabs=stt)

```json
{
  "2": {
    "model": "gpt-3.5-turbo",
    "start_text": "Hello, I am Dongbei Rabbit. How can I help you?",
    "prompt": "You are a knowledgeable and helpful intelligent robot named 'Dongbei Rabbit'. Your task is to chat with me using short conversations in Chinese, with each response not exceeding 50 characters!",
    "max_message_count": 20,
    "temperature": 0.7,
    "max_tokens": 800,
    "top_p": 0.95,
    "frequency_penalty": 0,
    "presence_penalty": 0,
    "voice_name":"zh-CN-liaoning-XiaobeiNeural",
    	"language":"zh-CN",
    	"stt_type":"azure-stt"
   }
}
```

If using the docker image version `v23.45.1.1` or above, you can also configure it like this:

```json
{
  "2": {
    "start_text": "Hello, I am Dongbei Rabbit. May I help you with anything?",
    "prompt": "You are a knowledgeable and helpful intelligent robot named 'Dongbei Rabbit'. Your task is to chat with me in a short dialogue format, speaking in Chinese and each response should not exceed 50 characters!",
    "max_message_count": 20,
    "stt_type": "azure-stt",
    "stt_config: {
        "language": "zh-CN"
    },
    "llm_config": {
        "model": gpt-3.5-turbo",
        temperature: 0.7,
        max_tokens: 800,
        top_p: 0.95,
        frequency_penalty: 0,
        presence_penalty: 0
    },
     tts_config ": {
         voice_name ": zh-CN-liaoning-XiaobeiNeural "
     }
   }
}
```

## Does the conversation not support context?

Modify the parameter `max_message_count` in `roles.json` to a value greater than 0, for example, change it to `10`, which means that context supports 10 conversations.
After modifying `roles.json`, execute the following command to restart the service in the folotoy-server-self-hosting directory for the changes to take effect.

```bash
sudo docker compose folotoy restart
```


## After several conversations, the toy no longer responds?

Each model has its own limit on the size of context tokens. If it exceeds the limit, you can press the current role button again to clear the context. You can also adjust the values of `max_message_count` and `max_tokens` to roughly limit the size of the context.

Modify the parameter `max_message_count` in `roles.json` to a value greater than 0, for example, change it to `10`. This means that the context supports 10 conversations. Then modify `max_tokens` to be `200`. After 10 rounds of conversation, the approximate size of the context is

```
10*200*2=4000
```

After making these modifications, the system will only keep the most recent 10 rounds of dialogue after more than 10 rounds of conversation.

After modifying `roles.json`, you need to execute the following command in the folotoy-server-self-hosting directory for the changes to take effect.

```bash
sudo docker compose folotoy restart
```

## Is the interval between voice conversations long or is there loss?

You can improve this situation by using AWS CloudFront CDN acceleration. The best way is to switch to a host with fast internet speed. If the ping value is below 200ms, you don't need to configure CDN.

CDN configuration video tutorials:

* [YouTube](https://www.youtube.com/watch?v=frfVUooWYIM)
* [Bilibili](https://www.bilibili.com/video/BV17G411m7rn/?spm_id_from=333.999.0.0&vd_source=c136ccc9c1aa8d8b8723abf57c945bf5)

## EMQX backend sees unknown client login?

Need to do some security configuration for EMQX, video tutorial.

* [YouTube](https://www.youtube.com/watch?v=3yW5260OTwY)
* [Bilibili](https://www.bilibili.com/video/BV1S94y1b74y)

## How to view server logs?

Execute the following command in the deployment directory:

```bash
sudo docker compose logs -f
```

Location of log files: `/var/lib/docker/containers/{container-id}/{container-id}-json.log`

Execute the command to view the first 12 characters of container-id.

```bash
sudo docker ps
```

<img src="https://github.com/FoloToy/folotoy-server-self-hosting/assets/1455685/6b75dab4-8f71-411f-a266-0ad43824df82" />

## How to modify the log level of the service program to DEBUG or INFO?

Modify the `docker-compose.yml` configuration, and after modification, refer to the previous steps to restart the container:

Change

```
LOG_LEVEL: INFO
```
to

```
LOG_LEVEL: DEBUG
```

## Why are my Docker timestamp logs different than my machine?

Docker container timezones default to UTC. To set the timezone for your container, use the `TZ` Environment Variable in your YML file. More information found at [Environment Variables](https://docs.teslamate.org/docs/configuration/environment_variables)