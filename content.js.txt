async function createPopup() {
  const link = document.createElement("link");
  link.rel = "stylesheet";
  link.href =
    "https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css";
  document.head.appendChild(link);

  windowURL = window.location.href;

  const popup = document.createElement("div");
  popup.style.backgroundColor = "white";
  popup.style.position = "fixed";
  popup.style.top = "5vh";
  popup.style.right = "20vw";
  popup.style.display = "flex";
  popup.style.justifyContent = "center";
  popup.style.alignItems = "center";
  popup.style.zIndex = "1000";
  popup.style.width = "60vw";
  popup.style.height = "40vw";
  popup.style.overflow = "hidden";
  popup.style.border = "1px solid black";
  popup.style.borderRadius = "10px";

  const exit = document.createElement("button");
  exit.innerHTML = "X";
  exit.style.position = "absolute";
  exit.style.top = "5px";
  exit.style.right = "5px";
  exit.style.backgroundColor = "transparent";
  exit.style.border = "none";
  exit.style.cursor = "pointer";
  exit.onclick = () => {
    popup.remove();
  };
  popup.appendChild(exit);

  const pack = document.createElement("img");
  let packURL = chrome.runtime.getURL("assets/Pack.png");
  pack.src = packURL;
  pack.style.height = "100%";
  pack.style.width = "auto";
  pack.style.objectFit = "cover";
  pack.style.borderRadius = "10px";
  pack.classList.add(
    "animate__animated",
    "animate__wobble",
    "animate__infinite"
  );
  pack.style.setProperty("--animate-duration", "1.75s");
  pack.style.cursor = "pointer";

  pack.addEventListener("click", () => {
    pack.remove();
    animation(popup, windowURL);
  });
  popup.appendChild(pack);
  document.body.appendChild(popup);
}

function findRarity(grade) {
  if (grade >= 95) {
    return "assets/TOTY.png";
  } else if (grade >= 90) {
    return "assets/Icon.png";
  } else if (grade >= 85) {
    return "assets/TOTW.png";
  } else if (grade >= 80) {
    return "assets/gold.png";
  } else if (grade >= 75) {
    return "assets/gold.png";
  } else if (grade >= 70) {
    return "assets/rare-silver.png";
  } else if (grade >= 65) {
    return "assets/common-silver.png";
  } else if (grade >= 60) {
    return "assets/rare-bronze.png";
  } else {
    return "assets/common-bronze.png";
  }
}

function getGrade(grade) {
  if (grade >= 93) {
    return "A";
  } else if (grade >= 90) {
    return "A-";
  } else if (grade >= 87) {
    return "B+";
  } else if (grade >= 83) {
    return "B";
  } else if (grade >= 80) {
    return "B-";
  } else if (grade >= 77) {
    return "C+";
  } else if (grade >= 73) {
    return "C";
  } else if (grade >= 70) {
    return "C-";
  } else if (grade >= 67) {
    return "D+";
  } else if (grade >= 63) {
    return "D";
  } else if (grade >= 60) {
    return "D-";
  } else {
    return "F";
  }
}

async function animate(element, animationClass, duration = 0) {
  return new Promise((resolve) => {
    element.classList.add("animate__animated", animationClass);
    if (duration) {
      element.style.setProperty("--animate-duration", `${duration}s`);
    }
    element.addEventListener("animationend", () => {
      element.classList.remove("animate__animated", animationClass);
      resolve();
    });
  });
}

async function animation(popup, window) {
  const header = document.querySelector(".mobile-header-title");
  let course = header.childNodes[1].innerHTML;
  if (course.length > 20) {
    let strlength = 20;
    while (course[strlength] !== " " && strlength < 30) {
      strlength++;
    }
    course = course.substring(0, strlength) + "...";
  }

  let assignment = document.querySelector(".submission-details-header__heading.h3").innerHTML;
  console.log(assignment);

  if (assignment.length > 20) {
    let strlength = 20;
    while (assignment[strlength] !== " " && strlength < 30) {
      strlength++;
    }
    assignment = assignment.substring(0, strlength) + "...";
  }

  const score = document.querySelector(".grade").innerHTML;
  const subDetails = document.querySelector(".bold");
  const untrimmed = subDetails.childNodes[2].nodeValue;
  const parts = untrimmed.split("/");
  const total = parts[1].trim();

  const rawGrade = (score / total) * 100;
  const letterGrade = getGrade(rawGrade);
  const rarity = findRarity(rawGrade);
  let textColor = "black";
  if (rarity.includes("TOTW")) {
    textColor = "gold";
  } else if (rarity.includes("TOTY")) {
    textColor = "white";
  }

  const courseDiv = document.createElement("div");
  courseDiv.style.position = "relative";
  courseDiv.style.overflow = "hidden";
  courseDiv.style.display = "flex";
  courseDiv.style.justifyContent = "center";
  courseDiv.style.alignItems = "center";
  courseDiv.style.height = "100%";
  courseDiv.style.width = "auto";
  courseDiv.style.fontSize = "2vw";

  const courseBadge = document.createElement("img");
  let badgeURL = chrome.runtime.getURL("assets/grey-badge.jpg");
  courseBadge.src = badgeURL;
  courseBadge.style.height = "100%";
  courseBadge.style.width = "100%";
  courseBadge.style.objectFit = "cover";

  const courseText = document.createElement("div");
  courseText.style.position = "absolute";
  courseText.style.zIndex = "1001";
  courseText.style.textAlign = "center";
  courseText.style.color = "white";
  courseText.style.top = "20%";
  courseText.style.width = "75%";
  courseText.style.height = "fit-content";
  courseText.style.maxHeight = "60%";
  courseText.style.maxWidth = "75%";

  const courseTitle = document.createElement("h1");
  courseTitle.innerHTML = "Course";
  courseTitle.style.fontWeight = "bold";
  courseTitle.style.textDecoration = "underline";

  const courseName = document.createElement("h1");
  courseName.innerHTML = course;
  courseName.style.textWrap = "wrap";

  courseText.appendChild(courseTitle);
  courseText.appendChild(courseName);
  courseDiv.appendChild(courseBadge);
  courseDiv.appendChild(courseText);

  const assignmentDiv = document.createElement("div");
  assignmentDiv.style.position = "relative";
  assignmentDiv.style.overflow = "hidden";
  assignmentDiv.style.display = "none";
  assignmentDiv.style.justifyContent = "center";
  assignmentDiv.style.alignItems = "center";
  assignmentDiv.style.height = "100%";
  assignmentDiv.style.width = "auto";
  assignmentDiv.style.fontSize = "2vw";
  assignmentDiv.style.visibility = "hidden";

  const assignmentBadge = document.createElement("img");
  let assBadgeUrl = chrome.runtime.getURL("assets/grey-badge.jpg");
  assignmentBadge.src = assBadgeUrl;
  assignmentBadge.style.height = "100%";
  assignmentBadge.style.width = "100%";
  assignmentBadge.style.objectFit = "cover";

  const assignmentText = document.createElement("div");
  assignmentText.style.position = "absolute";
  assignmentText.style.zIndex = "1001";
  assignmentText.style.textAlign = "center";
  assignmentText.style.color = "white";
  assignmentText.style.top = "20%";
  assignmentText.style.width = "75%";
  assignmentText.style.height = "fit-content";
  assignmentText.style.maxHeight = "60%";
  assignmentText.style.maxWidth = "75%";

  const assignmentTitle = document.createElement("h1");
  assignmentTitle.innerHTML = "Assignment";
  assignmentTitle.style.fontWeight = "bold";
  assignmentTitle.style.textDecoration = "underline";

  const assignmentName = document.createElement("h1");
  assignmentName.innerHTML = assignment;
  assignmentName.style.textWrap = "wrap";

  assignmentText.appendChild(assignmentTitle);
  assignmentText.appendChild(assignmentName);
  assignmentDiv.appendChild(assignmentBadge);
  assignmentDiv.appendChild(assignmentText);

  const card = document.createElement("div");
  card.style.position = "relative";
  card.style.overflow = "hidden";
  card.style.display = "none";
  card.style.justifyContent = "center";
  card.style.alignItems = "center";
  card.style.borderRadius = "20%";
  card.style.visibility = "hidden";
  card.style.backgroundColor = "transparent";
  popup.appendChild(card);

  const cardImg = document.createElement("img");
  let imageURL = chrome.runtime.getURL(rarity);
  cardImg.src = imageURL;
  cardImg.style.height = "40vw";
  cardImg.style.width = "auto";
  cardImg.style.borderRadius = "10%";
  cardImg.style.display = "block";

  const cardTop = document.createElement("div");
  cardTop.style.display = "flex";
  cardTop.style.flexDirection = "column";
  cardTop.style.alignItems = "center";
  cardTop.style.position = "absolute";
  cardTop.style.top = "40%";
  cardTop.style.left = "50%";
  cardTop.style.transform = "translate(-50%, -50%)";
  cardTop.style.textAlign = "center";
  cardTop.style.color = textColor;
  cardTop.style.fontWeight = "bold";

  const scoreText = document.createElement("div");
  scoreText.style.display = "flex";
  scoreText.style.flexDirection = "column";
  scoreText.style.fontSize = "2vw";
  scoreText.style.fontWeight = "bold";
  scoreText.style.fontStyle = "italic";
  scoreText.style.alignItems = "center";
  scoreText.style.paddingTop = "2vw";

  const myScore = document.createElement("h1");
  myScore.innerHTML = `${score}/${total}`;

  const myGrade = document.createElement("h1");
  myGrade.innerHTML = `${letterGrade}`;
  myGrade.style.height = "fit-content";
  myGrade.style.fontSize = "12vw";

  const cardBottom = document.createElement("div");
  cardBottom.style.position = "absolute";
  cardBottom.style.bottom = "4.5vw";
  cardBottom.style.left = "50%";
  cardBottom.style.transform = "translate(-50%, -50%)";
  cardBottom.style.textAlign = "center";
  cardBottom.style.color = textColor;
  cardBottom.style.fontSize = "1vw";
  cardBottom.style.fontWeight = "bold";
  cardBottom.style.display = "flex";
  cardBottom.style.flexDirection = "column";
  cardBottom.style.alignItems = "center";
  cardBottom.style.width = "90%";
  cardBottom.style.height = "fit-content";
  cardBottom.style.maxHeight = "20%";
  cardBottom.style.textWrap = "normal";
  cardBottom.style.fontWeight = "bold";

  const assCardBottom = document.createElement("h2");
  assCardBottom.innerHTML = assignment;
  assCardBottom.style.maxWidth = "90%";
  assCardBottom.style.textWrap = "normal";

  const courseCardBottom = document.createElement("h2");
  courseCardBottom.innerHTML = course;
  courseCardBottom.style.maxWidth = "90%";
  courseCardBottom.style.textWrap = "normal";
  courseCardBottom.style.paddingTop = "4%";

  card.appendChild(cardImg);
  card.appendChild(cardTop);
  card.appendChild(cardBottom);
  cardTop.appendChild(scoreText);
  scoreText.appendChild(myScore);
  scoreText.appendChild(myGrade);
  cardBottom.appendChild(assCardBottom);
  cardBottom.appendChild(courseCardBottom);

  popup.appendChild(courseDiv);
  popup.appendChild(assignmentDiv);
  popup.appendChild(card);

  cardImg.onload = () => {
    const rgb = getRGB(cardImg);
    card.style.boxShadow = `0 0 30px 30px rgba(${rgb.r}, ${rgb.g}, ${rgb.b})`;
  };

  const elements = popup.querySelectorAll("*");
  elements.forEach((element) => {
    element.style.margin = "0";
    element.style.padding = "0";
  });

  await animate(courseDiv, "animate__backInLeft");
  await new Promise((resolve) => setTimeout(resolve, 750));
  await animate(courseDiv, "animate__backOutRight", 1.5);

  courseDiv.remove();
  assignmentDiv.style.display = "flex";
  assignmentDiv.style.visibility = "visible";

  await animate(assignmentDiv, "animate__backInLeft");
  await new Promise((resolve) => setTimeout(resolve, 750));
  await animate(assignmentDiv, "animate__backOutRight", 1.5);

  assignmentDiv.remove();
  card.style.display = "flex";
  card.style.visibility = "visible";
  await animate(card, "animate__fadeIn", 4);
}

function getRGB(imgEl) {
  var blockSize = 5, // only visit every 5 pixels
    defaultRGB = { r: 0, g: 0, b: 0.5 }, // for non-supporting envs
    canvas = document.createElement("canvas"),
    context = canvas.getContext && canvas.getContext("2d"),
    data,
    width,
    height,
    i = -4,
    length,
    rgb = { r: 0, g: 0, b: 0 },
    count = 0;

  if (!context) {
    return defaultRGB;
  }

  height = canvas.height =
    imgEl.naturalHeight || imgEl.offsetHeight || imgEl.height;
  width = canvas.width = imgEl.naturalWidth || imgEl.offsetWidth || imgEl.width;

  context.drawImage(imgEl, 0, 0);

  try {
    data = context.getImageData(0, 0, width, height);
  } catch (e) {
    /* security error, img on diff domain */
    return defaultRGB;
  }

  length = data.data.length;

  while ((i += blockSize * 4) < length) {
    ++count;
    rgb.r += data.data[i];
    rgb.g += data.data[i + 1];
    rgb.b += data.data[i + 2];
  }

  // ~~ used to floor values
  rgb.r = ~~(rgb.r / count);
  rgb.g = ~~(rgb.g / count);
  rgb.b = ~~(rgb.b / count);

  return rgb;
}

createPopup();
