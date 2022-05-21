# audio and video summarization with api in python

**1. To work with API first we have to get our API key** <br>
**2. We have to gather our video or audio that we going to use as input**

> for using this API we have to create an account in [free assemblyAI account](https://www.assemblyai.com/)<br>
> Once you create your account go to the home tab and look for a box named **“Integrate the API”** to copy your API key.<br>
> I have used this [motivationl video](https://www.youtube.com/watch?v=BxY_eJLBflk) length is 8 min 

## step 1
**here we specify the inputs like api key upload_endpoint and filename** 
```python 
import requests
filename="./Images/Put_God.mp3"
api_key="c3bdd9c94ae843f58a20041b107b0bad"
upload_endpoint = 'https://api.assemblyai.com/v2/upload'
```
## step 2
**here we use a function named read file and use post request to upload files in endpoint**
``` python
def read_file(filename, chunk_size=5242880):
    with open(filename, 'rb') as f:
        while True:
            data = f.read(chunk_size)
            if not data:
                break
            yield data
headers = {'authorization': api_key,'content-type': 'application/json'}
response = requests.post(upload_endpoint,headers=headers,data=read_file(filename))
audio_url = response.json()['upload_url']
```
**In the end, we use the response of our request to get the URL where the audio file was uploaded. We store all of this in a variable named audio_url that we’ll use in the next step.**
## step 3
**here will post the request get the transcript id**
```python
transcript_endpoint = "https://api.assemblyai.com/v2/transcript"

response = requests.post(transcript_endpoint,
                         headers=headers,
                         json={
                             "audio_url": audio_url,
                             "auto_chapters": True
                         })
transcript_id = response.json()['id']
```
**this transcript id will be used in step 4**
## step 4
**To get our transcript and summary, first, we use the transcript_endpoint and transcript_id to create a variable named polling_endpoint. We’ll send a get request periodically to this endpoint to check for the status of the request we sent in step 3**
```python
import os
import sys
import time
import json

polling_endpoint = os.path.join(transcript_endpoint, transcript_id)
status = ''
while status != 'completed':
    response_result = requests.get(
        polling_endpoint,
        headers=headers
    )
    status = response_result.json()['status']
    print(f'Status: {status}')

    if status == 'error':
        sys.exit('Audio file failed to process.')
    elif status != 'completed':
        time.sleep(10)

if status == 'completed':
    filename = transcript_id + '.txt'
    with open(filename, 'w') as f:
        f.write(response_result.json()['text'])

    filename = transcript_id + '_chapters.json'
    with open(filename, 'w') as f:
        chapters = response_result.json()['chapters']
        json.dump(chapters, f, indent=4)

    print('Transcript Saved')
```
**Only when the status is set to completed, we save the transcript in a text file and the summary in a JSON file. We use the variable transcript_id as the name for these two files.**

>**Now we are done**<br>
>**check the directory for json and text files that shows summeraize version of input video**
## output for my video input
**.json output**
```json
[
    {
        "summary": "It's been 40 years since he flunked out of College at a 17 grade point average. He has traveled the world and spoke to millions of people. His mother's advice to him has stayed with him ever since. He believes in having goals, discipline and consistency to achieve them. That's where the success is.",
        "headline": "It's been 40 years since he flunked out of College at a 17 grade point average.",
        "gist": "The gift of knowledge.",
        "start": 10090,
        "end": 382880
    },
    {
        "summary": "Tonight put your slippers under the bed and say thank you for Grace, mercy, understanding, wisdom, love, humility, peace, prosperity and parents true desire in the heart for anything good is proof to you that it's yours already aspire to make a difference and help others.",
        "headline": "Tonight put your slippers under the bed and say thank you to your parents.",
        "gist": "Say thank you.",
        "start": 383930,
        "end": 502240
    }
]
```
**Text output**
``` text 
On one. Put God. Put God first in everything you do. Everything that you think you see in me, everything that I've accomplished, everything that you 
think think I have. And I have a few things. Everything that I have is by the Grace of God. Understand that. It's a gift. 40 years ago, March 2775. It 
was 40 years ago. Just this past March, I was flunking out of College at a 1.7 grade point average. I hope none of you can relate at a 1.7 grade point 
average. I was sitting in my mother's beauty shop. They still call it beauty shop. Now. What do they call it? Yeah, I was sitting in the beauty Pollock.
I was sitting in my mother's beauty Pollock. And I'm looking in the mirror. And I see behind me this woman under the dryer. And every time she looked up,
every time I looked up, she was looking at me. Just look at me in the eye. I didn't know who she was. And she said, Somebody, give me a pen. Give me a 
pencil. I have a prophecy. March 27, 1975. She said, Boy, you are going to travel the world and speak to millions of people. Now, mind you, I flunked out
of College. I'm thinking about joining the army. I didn't know what I was going to do. And she's telling me, I'm going to travel the world and speak to
millions of people. Well, I have traveled the world, and I have spoke to millions of people. But that's not the most important thing. The success that
I had. The most important thing is that what she taught me. And what she told me that day has stayed with me since. I've been protected. I've been
directed. I've been corrected. I've kept God in my life and has kept me humble. I didn't always stick with him, but he always stuck with me. So stick
with Him in everything you do. If you think you want to do what you think I've done, then do what I've done and stick with God. Number two, fail big.
That's right. Fail big. Today's the beginning of the rest of your life. And it can be very frightening. It's a new world out there. It's a mean world out
there. And you only live once. So do what you feel passionate about. Passionate about. Take chances professionally. Don't be afraid to fail. As an old IQ
test was nine dots. And you had to draw five lines with a pencil within these nine dots. Without lifting the pencil. The only way to do it was to go
outside the box. So don't be afraid to go outside the box. Don't be afraid to think outside the box. Don't be afraid to fail big. To dream big. But
remember, dreams without goals are just dreams. And they ultimately fuel disappointment. So have dreams, but have goals. Life goals, yearly goals,
monthly goals, daily goals. I try to get myself a goal everyday. Sometimes just to not curse somebody out. Simple goals, but have goals. And understand
that to achieve these goals, you must apply discipline and consistency. In order to achieve your goals, you must apply discipline, which you've already
done, and consistency every day, not just one Tuesday and miss a few days. You have to work at it every day. You have to plan every day. You've heard the
saying, we don't plan to fail. We fail to plan. Hard work works. Working really hard is what successful people do. And in this text, tweet twerk world
that you've grown up in, remember, just because you're doing a lot more doesn't mean you're getting a lot more done. Don't confuse movement with
progress. My mother told me. Yeah, because you can run in place all the time and never get anywhere. So continue to strive, continue to have goals.
Continue to progress. Number three, you'll never see a, Uhaul, behind a hearse. I'll say it again. You'll never see a, Uhaul, behind a hearse. I don't
care how much money you make, you can't take it with you. And it's not how much you have. It's what you do with what you have. We all have different
talents. Some of you will be doctors, some lawyers, some scientists, some educators, some nurses, some teachers. Okay? Some preachers. The most selfish
thing you can do in this world is help someone else. Why is it selfish? Because the gratification, the goodness that comes to you, the good feeling, the
good feeling that I get from helping others. Nothing's better than that. Not jewelry, not big house. I have not the cars, but it's the joy. That's where
the joy is in helping others. That's where the success is. Finally, I pray that you put your slippers way under the bed tonight so that when you wake up
in the morning, you have to get on your knees to reach them. And while you're down there, say thank you for Grace. Thank you for mercy. Thank you for
understanding. Thank you for wisdom. Thank you for parents. Thank you for love. Thank you for kindness. Thank you for humility. Thank you for peace.
Thank you for prosperity. Say thank you in advance for what's already yours. That's how I live my life. That's why I am one of the reasons why I am 
today. Say thank you in advance for what is already yours. True desire in the heart for anything good is God's proof to you. Sit beforehand to indicate 
that it's yours already. I'll say it again. True desire in the heart. That itch. That you have whatever it is you want to do. That thing that you want to
do to help others and to grow and to make money. That desire. That itch. That's God's proof to you. Sent beforehand already to indicate that it's yours
and anything you want good, you can have. So claim it. Work hard to get it. When you get it, reach back. Pull someone else up each one, teach one don't 
just aspire to make a living. Aspire to make a difference.
```





