---
layout: default
title: Publications
---

# Publications

<div id="orcid-publications">Loading publications...</div>

<script>
async function fetchORCIDPublications() {
  const orcidId = "0000-0003-1342-6476";
  const apiUrl = `https://pub.orcid.org/v3.0/${orcidId}/works`;

  try {
    const response = await fetch(apiUrl, {
      headers: {
        "Accept": "application/json"
      }
    });

    if (!response.ok) {
      throw new Error(`ORCID API error: ${response.status}`);
    }

    const data = await response.json();
    const publications = data.group || [];

    let html = "<ul>";

    publications.forEach((pub) => {
      const summary = pub["work-summary"]?.[0];

      if (!summary) return;

      const workType = summary["type"] || "";
      const title = summary["title"]?.["title"]?.["value"] || "Untitled";
      const journalName = summary["journal-title"]?.["value"] || "";
      const url = summary["url"]?.["value"] || "#";

      const publicationDate = summary["publication-date"];
      const year = publicationDate?.["year"]?.["value"] || "n.d.";

      const doi = summary["external-ids"]?.["external-id"]
        ?.find(id => id["external-id-type"] === "doi")
        ?.["external-id-value"] || "";

      const link = url !== "#"
        ? `<a href="${url}" target="_blank" rel="noopener noreferrer">link</a>`
        : "";

      const doiText = doi
        ? ` DOI: ${doi}`
        : "";

      html += `
        <li>
          ${year} — ${title}
          ${journalName ? ` — <i>${journalName}</i>` : ""}
          ${workType ? ` — (${workType})` : ""}
          ${doiText}
          ${link ? ` — ${link}` : ""}
        </li>
      `;
    });

    html += "</ul>";

    document.getElementById("orcid-publications").innerHTML = html;

  } catch (error) {
    console.error("Error fetching ORCID publications:", error);
    document.getElementById("orcid-publications").innerText =
      "Unable to load publications.";
  }
}

fetchORCIDPublications();
</script>
