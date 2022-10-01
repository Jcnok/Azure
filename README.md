# Como utilizar o serviço "Cognitive Services" da AZURE para converter áudio em texto.

## O primeiro passo é criar uma conta gratuíta e criar o serviço, Speech service. 
Segue a documentação conforme o [link](https://learn.microsoft.com/pt-br/azure/cognitive-services/speech-service/how-to-recognize-speech?pivots=programming-language-python)

![img](https://github.com/Jcnok/Azure/blob/main/image/cognitive_service.jpg?raw=true)

## Após criar o serviço basta copiar a chave e a região para conexão com o python.
![img](https://github.com/Jcnok/Azure/blob/main/image/chave.jpg?raw=true)

## Feito isso podemos criar a conexão com o jupyter.


```python
# Instalação do pacote para conexão
!pip install azure-cognitiveservices-speech -q
```


```python
# importando as libs que serão utilizadas.
import azure.cognitiveservices.speech as speechsdk
import time
```


```python
# Configuração da chave e região.
speech_key, service_region = "Sua chave azure", "sua região azure"
```

## Teste de conexão utilizando o microfone fala em Inglês.


```python
def recognize_from_mic():
	#Find your key and resource region under the 'Keys and Endpoint' tab in your Speech resource in Azure Portal
	#Remember to delete the brackets <> when pasting your key and region!
    speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
    
    #Asks user for mic input and prints transcription result on screen
    print("Speak into your microphone.")
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

recognize_from_mic()
```

    Speak into your microphone.
    How are you? I am from Brazil and you are you from?
    

## Usando o microfone para converter o áudio  português em texto:


```python
def recognize_from_microphone():
    speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
    speech_config.speech_recognition_language="pt-BR" #"en-US"

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)

    print("Fale no seu microfone.")
    speech_recognition_result = speech_recognizer.recognize_once_async().get()

    if speech_recognition_result.reason == speechsdk.ResultReason.RecognizedSpeech:
        print("Recognized: {}".format(speech_recognition_result.text))
    elif speech_recognition_result.reason == speechsdk.ResultReason.NoMatch:
        print("No speech could be recognized: {}".format(speech_recognition_result.no_match_details))
    elif speech_recognition_result.reason == speechsdk.ResultReason.Canceled:
        cancellation_details = speech_recognition_result.cancellation_details
        print("Speech Recognition canceled: {}".format(cancellation_details.reason))
        if cancellation_details.reason == speechsdk.CancellationReason.Error:
            print("Error details: {}".format(cancellation_details.error_details))
            print("Did you set the speech resource key and region values?")

recognize_from_microphone()
```

    Fale no seu microfone.
    Recognized: Testando 123, testando. Se a fala reconhece como um texto.
    

## Configurando para converter um arquivo de áudio em texto:


```python
def recognize_from_microphone():
    speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
    speech_config.speech_recognition_language="pt-BR" #"en-US"

    audio_config = speechsdk.audio.AudioConfig(filename="Gravando.wav")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)

    print("Lendo a arquivo de áudio.")
    speech_recognition_result = speech_recognizer.recognize_once_async().get()

    if speech_recognition_result.reason == speechsdk.ResultReason.RecognizedSpeech:
        print("Recognized: {}".format(speech_recognition_result.text))
    elif speech_recognition_result.reason == speechsdk.ResultReason.NoMatch:
        print("No speech could be recognized: {}".format(speech_recognition_result.no_match_details))
    elif speech_recognition_result.reason == speechsdk.ResultReason.Canceled:
        cancellation_details = speech_recognition_result.cancellation_details
        print("Speech Recognition canceled: {}".format(cancellation_details.reason))
        if cancellation_details.reason == speechsdk.CancellationReason.Error:
            print("Error details: {}".format(cancellation_details.error_details))
            print("Did you set the speech resource key and region values?")

recognize_from_microphone()
```

    Fale no seu microfone.
    Recognized: Testando a configuração do áudio para conversão de fala em texto.
    

## configurando para converter o arquivo de áudio inteiro.


```python
# Criando uma lista para salvar o texto gerado.
lista = []
```


```python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
speech_config.speech_recognition_language="pt-BR" #"en-US"
audio_config = speechsdk.audio.AudioConfig(filename="Gravando_2.wav")
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
done = False
def stop_cb(evt):
    #print('CLOSING')
    speech_recognizer.stop_continuous_recognition()
    global done
    done = True
speech_recognizer.session_started.connect(lambda evt: print('Incializando a sessão'))
speech_recognizer.recognized.connect(lambda evt:lista.append(evt.result.text))
speech_recognizer.session_stopped.connect(lambda evt: print('Finalizando a sessão'))
speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
# inicializando o speech   
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

    Incializando a sessão
    Finalizando a sessão
    


```python
#visualizando a lista:
lista
```




    ['Neste guia de instruções, você saberá como reconhecer e transcrever a fala humana, geralmente chamada de conversão de fala intenso.',
     'Para chamar o serviço de fala usando um SDK de fala, você precisa criar uma instância.',
     'Essa classe inclui informações sobre sua assinatura, uma chave de fala e localização.',
     'Um ponto de extremidade host ou token de autorização associado.']



* **Podemos utilizar a lista para criar algum estudo de nlp.**
* **Um exemplo seria identificar se os analistas estão utilizando palavras ou frases inadequadas durante um atendimento**.
* **Imaginem um modelo capaz de identificar um atendimento inadequado de forma automática**
* **Podemos ir mais além, por exemplo identificar um possível indicio de fraude em um atendimento.**.
* **Podemos identificar um padrão de acordo com as informações coletadas dos analistas, para uma futura melhoria.**
* **Podemos facílmente classificar as dores dos clientes, visando melhorias de forma mais acertiva.**
* **São várias as alternativas que podemos utilizar, essas são apenas algumas que me vem a mente.**


```python
#exemplo simples para identificar uma palavra.
palavra = 'extremidade'
for i in range(len(lista)):
    if palavra in (lista[i]):
        print(f"a {palavra} está na lista de indice {i}")
    else:
        print("A palavra não foi encontrada na lista")
```

    A palavra não foi encontrada na lista
    A palavra não foi encontrada na lista
    A palavra não foi encontrada na lista
    A palavra não foi encontrada na lista
    A palavra não foi encontrada na lista
    a extremidade está na lista de indice 5
    

# Convertendo texto em Áudio:


```python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```


```python
speech_config.speech_recognition_language="pt-BR" #"en-US" #escrita
speech_config.speech_synthesis_language="pt-BR" #fala
```


```python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config)
```


```python
print("Type some text that you want to speak...")
text = input()
```

    Type some text that you want to speak...
    Testando o sistema
    


```python
text = lista[0]
```


```python
text
```




    'Neste guia de instruções, você saberá como reconhecer e transcrever a fala humana, geralmente chamada de conversão de fala intenso.'




```python
result = speech_synthesizer.speak_text_async(text).get()
```


```python
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized to speaker for text [{}]".format(text))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
    print("Did you update the subscription info?")
```

    Speech synthesized to speaker for text [Neste guia de instruções, você saberá como reconhecer e transcrever a fala humana, geralmente chamada de conversão de fala intenso.]
    


```python

```
