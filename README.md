**Nome:** Mateus Figueiredo

**Matrícula:** 2017014430

**Projeto:** Youtube-dl (https://github.com/ytdl-org/youtube-dl)

O youtube-dl é uma ferramenta de linha de comando que permite fazer o download de vídeos/áudios do Youtube e de outras plataformas da internet. O desenvolvimento dessa ferramenta foi realizado em Python e os testes apresentados nesse trabalho utilizam a biblioteca unittest.

**Teste 1**
```python
def test_prefer_free_formats(self):
    # Same resolution => download webm
    ydl = YDL()
    ydl.params['prefer_free_formats'] = True
    formats = [
        {'ext': 'webm', 'height': 460, 'url': TEST_URL},
        {'ext': 'mp4', 'height': 460, 'url': TEST_URL},
    ]
    info_dict = _make_result(formats)
    yie = YoutubeIE(ydl)
    yie._sort_formats(info_dict['formats'])
    ydl.process_ie_result(info_dict)
    downloaded = ydl.downloaded_info_dicts[0]
    self.assertEqual(downloaded['ext'], 'webm')
```

Primeiro é criado um objeto da classe YDL que contém funcionalidades da ferramenta. Em seguida, a opção a ser testada (preferir formatos gratuitos) é definida como True. 

Depois, é criado um dicionário com as duas opções de formatos gratuitos, sendo uma entrada no dicionário para cada formato (webm e mp4). Assim, são chamadas as funções auxiliares para realização da conexão com o youtube e para o download em um dos formatos encontrados.

Por padrão, caso as duas resoluções sejam iguais, o programa deve dar preferência ao webm, assim, como pode ser observado na última linha do código, o assert só será verdadeiro se o formato baixado for o webm.

**Teste 2**
```python
def test_subtitles(self):
        def s_formats(lang, autocaption=False):
            return [{
                'ext': ext,
                'url': 'http://localhost/video.%s.%s' % (lang, ext),
                '_auto': autocaption,
            } for ext in ['vtt', 'srt', 'ass']]
        subtitles = dict((l, s_formats(l)) for l in ['en', 'fr', 'es'])
        auto_captions = dict((l, s_formats(l, True)) for l in ['it', 'pt', 'es'])
        info_dict = {
            'id': 'test',
            'title': 'Test',
            'url': 'http://localhost/video.mp4',
            'subtitles': subtitles,
            'automatic_captions': auto_captions,
            'extractor': 'TEST',
        }

        def get_info(params={}):
            params.setdefault('simulate', True)
            ydl = YDL(params)
            ydl.report_warning = lambda *args, **kargs: None
            return ydl.process_video_result(info_dict, download=False)

        result = get_info()
        self.assertFalse(result.get('requested_subtitles'))
        self.assertEqual(result['subtitles'], subtitles)
        self.assertEqual(result['automatic_captions'], auto_captions)

```
Primeiro é definida uma função com objetivo de gerar um dicionário com as combinações de idiomas, formato e extensões das legendas. 

Depois são geradas algumas combinações de idiomas para as legendas e se as legendas serão automáticas ou não.

Por fim, um dicionário com todas essas informações geradas é enviado para uma segunda função auxiliar que vai processar os resultados dos vídeos de acordo com o dicionário criado anteriormente.

**Teste 3**
```python
def test_youtube_extract(self):
    assertExtractId = lambda url, id: self.assertEqual(YoutubeIE.extract_id(url), id)
    assertExtractId('http://www.youtube.com/watch?&v=BaW_jenozKc', 'BaW_jenozKc')
    assertExtractId('https://www.youtube.com/watch?&v=BaW_jenozKc', 'BaW_jenozKc')
    assertExtractId('https://www.youtube.com/watch?feature=player_embedded&v=BaW_jenozKc', 'BaW_jenozKc')
    assertExtractId('https://www.youtube.com/watch_popup?v=BaW_jenozKc', 'BaW_jenozKc')
    assertExtractId('http://www.youtube.com/watch?v=BaW_jenozKcsharePLED17F32AD9753930', 'BaW_jenozKc')
    assertExtractId('BaW_jenozKc', 'BaW_jenozKc')

```
O teste 3 é bastante simples, seu intuito é verificar se o identificador do vídeo extraído pela função extract_id da classe YoutubeIE é o correto. Essa verificação é feita em diversos cenários, como, por exemplo: um vídeo originado de um popup, vídeo proveniente de um embbeded player, etc.