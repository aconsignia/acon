let availableUnits = {};
let unitSpeedSettings;
$.when($.get('/interface.php?func=get_unit_info')).done(function(xml) {
    $(xml).find('config').children().each((index, unit) => {
        availableUnits[$(unit).prop('nodeName')] = $(unit).find('speed').text();
    });
    unitSpeedSettings = availableUnits;
}).then(() => {
    console.log("received data");
    console.log(unitSpeedSettings);
    // get all report information

    getReportInformation(unitSpeedSettings);
});


function getReportInformation(unitSpeedSettings) {
    var attackVillage = {
        "name": $("#attack_info_att .village_anchor a")[0].innerText.trim(),
        "coordinates": $("#attack_info_att .village_anchor a")[0].innerText.trim().match(/\d+\|\d+/)[0],
    }

    var defenseVillage = {
        "name": $("#attack_info_def .village_anchor a")[0].innerText.trim(),
        "coordinates": $("#attack_info_def .village_anchor a")[0].innerText.trim().match(/\d+\|\d+/)[0],
    }

    var distance = calculateDistance(attackVillage.coordinates, defenseVillage.coordinates);
    let battleTime = findBattleTime();

    let units = {
        "spear": parseInt($("#attack_info_att_units .unit-item-spear")[0].innerText.trim()),
        "sword": parseInt($("#attack_info_att_units .unit-item-sword")[0].innerText.trim()),
        "axe": parseInt($("#attack_info_att_units .unit-item-axe")[0].innerText.trim()),
        "spy": parseInt($("#attack_info_att_units .unit-item-spy")[0].innerText.trim()),
        "light": parseInt($("#attack_info_att_units .unit-item-light")[0].innerText.trim()),
        "heavy": parseInt($("#attack_info_att_units .unit-item-heavy")[0].innerText.trim()),
        "ram": parseInt($("#attack_info_att_units .unit-item-ram")[0].innerText.trim()),
        "catapult": parseInt($("#attack_info_att_units .unit-item-catapult")[0].innerText.trim()),
        "knight": parseInt($("#attack_info_att_units .unit-item-knight")[0].innerText.trim()),
        "snob": parseInt($("#attack_info_att_units .unit-item-snob")[0].innerText.trim()),
    }

    calculateBacktimeTime(units, unitSpeedSettings, distance, battleTime);
}

function findBattleTime() {
    var td = document.querySelectorAll("#content_value .vis td");
    var battleTimeLabelTD = [...td].filter(e => e.innerText == "Battle time");
    var battleTime = battleTimeLabelTD[0].nextElementSibling.innerText.trim()
    return Date.parse(battleTime);
}

// sort object by value descending
// return object
function sortObject(obj) {
    var sortable = [];
    for (var key in obj)
        if (obj.hasOwnProperty(key))
            sortable.push([key, obj[key]]);
    sortable.sort(function(a, b) {
        return b[1] - a[1];
    });
    return sortable;
}

// return slowest unit in the attacking force
function findSlowestUsedUnit(units, unitSpeeds) {
    unitSpeedDesc = sortObject(unitSpeeds);
    for (let i = 0; i < unitSpeedDesc.length; i++) {
        if (units[unitSpeedDesc[i][0]] > 0) {
            console.log("SLOWEST SPEED: " + unitSpeedDesc[i][0])
            return unitSpeedDesc[i][0];
        }
    }
}

// calculate distances between two coordinates
function calculateDistance(to, from) {
    var target = extractCoords(to).match(/(\d+)\|(\d+)/);
    var source = extractCoords(from).match(/(\d+)\|(\d+)/);
    var fields = Math.sqrt(Math.pow(source[1] - target[1], 2) + Math.pow(source[2] - target[2], 2));

    return fields;
}

//extract coordinates out of text
function extractCoords(src) {
    var loc = src.match(/\d+\|\d+/ig);
    return (loc ? loc[loc.length - 1] : null);
}

function calculateBacktimeTime(units, unitSpeedSettings, distance, battleTime) {
    let unitType = findSlowestUsedUnit(units, unitSpeedSettings);
    console.log(battleTime);
    let unixLandDate = battleTime + (unitSpeedSettings[unitType] * distance * 60 * 1000)
    console.log(unixLandDate);
    if (unixLandDate % 1 > 0.5) {
        // round up
        unixLandDate += 1000;
    }
    let landDate = new Date(Math.floor(unixLandDate)).toLocaleString('en-GB');
    Dialog.show("content", `<div>
        <p>Battle time: ${new Date(battleTime).toLocaleString('en-GB')}</p>
        <p>Slowest unit in attack: ${unitType}<p>
        <p>Distance to village: ${distance}</p>
        <p>Unit runtime per tile: ${unitSpeedSettings[unitType]}</p>
        <p>Expected runtime: ${toHoursAndMinutes(unitSpeedSettings[unitType] * distance)}</p>
        <p>Backtime time: ${landDate}</p>
    </div>`);
    console.log(landDate);
}

function toHoursAndMinutes(totalMinutes) {
    const minutes = Math.floor(totalMinutes % 60);
    const hours = Math.floor(totalMinutes / 60);

    return `${padTo2Digits(hours)}:${padTo2Digits(minutes)}`;
}

function padTo2Digits(num) {
    return num.toString().padStart(2, '0');
}
