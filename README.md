const { addonBuilder } = require("stremio-addon-sdk");
const fetch = require("node-fetch");
const cheerio = require("cheerio");

const addon = new addonBuilder({
  id: "org.youkid.stremio",
  version: "1.0.0",
  name: "YouKid Content",
  description: "Fetch content from YouKid website",
  resources: ["catalog", "meta"],
  types: ["movie"],
  catalogs: [
    {
      type: "movie",
      id: "youkid_catalog",
      name: "YouKid Catalog",
    },
  ],
});

async function fetchMoviesFromYouKid() {
  const url = "https://www.youkid.co.il/";
  const res = await fetch(url);
  const html = await res.text();
  const $ = cheerio.load(html);
  const movies = [];

  $(".movie-item").each((i, elem) => {
    const title = $(elem).find(".title").text().trim();
    const poster = $(elem).find("img").attr("src");
    const id = `youkid_${i}`;

    if (title) {
      movies.push({
        id: id,
        type: "movie",
        name: title,
        poster: poster,
      });
    }
  });

  return movies;
}

addon.defineCatalogHandler(async ({ type, id }) => {
  if (type === "movie" && id === "youkid_catalog") {
    const metas = await fetchMoviesFromYouKid();
    return { metas };
  }
  return { metas: [] };
});

addon.defineMetaHandler(async ({ type, id }) => {
  return null;
});

module.exports = addon.getInterface();

