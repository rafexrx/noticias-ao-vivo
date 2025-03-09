# noticias-ao-vivo
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Notícias ao Vivo</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #000;
            color: #fff;
            font-family: Arial, sans-serif;
            text-align: center;
            overflow: hidden;
        }
        .container {
            width: 100vw;
            height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }
        .news {
            width: 90%;
            max-width: 500px;
            height: 80vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            padding: 20px;
            text-align: center;
        }
        .news h1 {
            font-size: 2rem;
            margin-bottom: 20px;
        }
        .news p {
            font-size: 1.2rem;
        }
        .news a {
            color: white;
            text-decoration: underline;
        }
        .news img {
            width: 100%;
            height: auto;
            max-height: 300px;
            border-radius: 10px;
            margin-bottom: 15px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="news" id="news-container">
            <h1>Carregando notícias...</h1>
            <p>Aguarde um momento.</p>
        </div>
    </div>

    <script>
        async function fetchNews() {
            try {
                let response = await fetch('https://api.allorigins.win/get?url=https://g1.globo.com/rss/g1/df');
                let data = await response.json();
                let parser = new DOMParser();
                let xml = parser.parseFromString(data.contents, 'text/xml');
                let items = xml.querySelectorAll('item');
                
                return Array.from(items).map(item => {
                    let title = item.querySelector('title').textContent;
                    let link = item.querySelector('link').textContent;
                    let description = item.querySelector('description').textContent;
                    
                    // Extrair URL da imagem da descrição (caso disponível)
                    let imgMatch = description.match(/<img src=\"(.*?)\"/);
                    let imageUrl = imgMatch ? imgMatch[1] : '';
                    
                    return { title, link, imageUrl };
                });
            } catch (error) {
                console.error('Erro ao buscar notícias', error);
                return [];
            }
        }

        async function updateNews() {
            let articles = await fetchNews();
            let newsContainer = document.getElementById('news-container');
            let index = 0;

            function showNews() {
                if (articles.length > 0) {
                    let article = articles[index];
                    newsContainer.innerHTML = `
                        ${article.imageUrl ? `<img src="${article.imageUrl}" alt="Imagem da notícia">` : ''}
                        <h1>${article.title}</h1>
                        <p><a href="${article.link}" target="_blank">Leia mais</a></p>
                    `;
                    index = (index + 1) % articles.length;
                } else {
                    newsContainer.innerHTML = '<h1>Sem notícias disponíveis</h1>';
                }
            }

            showNews();
            setInterval(showNews, 5000); // Alterna as notícias a cada 5 segundos
        }

        updateNews();
    </script>
</body>
</html>
