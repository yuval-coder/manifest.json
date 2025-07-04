# manifest.json
https://https://www.youkid.co.il//manifest.json
[manifest.json](https://github.com/user-attachments/files/21068330/manifest.json)
# Welcome to GitHub Desktop!
const axios = require('axios');
const cheerio = require('cheerio');
const fs = require('fs');

const urls = [
  https://www.youkid.co.il/talents/yuval
https://www.youkid.co.il/talents/riko
https://www.youkid.co.il/talents/meni
https://www.youkid.co.il/talents/sapir
https://www.youkid.co.il/talents/miki
https://www.youkid.co.il/talents/michal
https://www.youkid.co.il/talents/haim
https://www.youkid.co.il/talents/kogomelo
https://www.youkid.co.il/talents/kofiko
https://www.youkid.co.il/talents/naama
https://www.youkid.co.il/talents/ofer_maor
https://www.youkid.co.il/talents/tzipi
https://www.youkid.co.il/talents/meital
https://www.youkid.co.il/talents/livluvit
https://www.youkid.co.il/talents/royboy
https://www.youkid.co.il/talents/gili_ba
https://www.youkid.co.il/talents/hilali
https://www.youkid.co.il/talents/yaeli
https://www.youkid.co.il/talents/ronen
https://www.youkid.co.il/talents/ester
https://www.youkid.co.il/talents/vardinon
https://www.youkid.co.il/talents/nati
https://www.youkid.co.il/talents/rinat
https://www.youkid.co.il/talents/tuvia
https://www.youkid.co.il/talents/yanai_michal
https://www.youkid.co.il/talents/tal
https://www.youkid.co.il/talents/yaniv
https://www.youkid.co.il/talents/liron
https://www.youkid.co.il/talents/katzefetoot
https://www.youkid.co.il/talents/inballerina
https://www.youkid.co.il/talents/moshe_datz
https://www.youkid.co.il/search?query=%D7%A8%D7%95%D7%A0%D7%99
https://www.youkid.co.il/search?query=%D7%A4%D7%A1%D7%98%D7%99%D7%92%D7%9C

];

async function fetchAndSave() {
  const file = fs.createWriteStream("youkid_data.jsonl", { flags: 'w' });

  for (const url of urls) {
    try {
      const res = await axios.get(url);
      const $ = cheerio.load(res.data);

      $(".video-item, .post").each((i, el) => {
        const title = $(el).find("h2, h3").text().trim();
        const poster = $(el).find("img").attr("src");
        const link = $(el).find("a").attr("href");
        const id = "yokid_" + encodeURIComponent(link);

        if (title && poster && link) {
          const meta = {
            id,
            type: "movie",
            name: title,
            poster: poster.startsWith('http') ? poster : 'https://www.youkid.co.il' + poster,
            description: `תוכן מיוקיד: ${title}`
          };
          file.write(JSON.stringify(meta) + "\n");
        }
      });

    } catch (err) {
      console.error("שגיאה בטעינת URL:", url, err.message);
    }
  }

  file.end(() => {
    console.log("✅ הקובץ youkid_data.jsonl נוצר בהצלחה!");
  });
}

fetchAndSave();
