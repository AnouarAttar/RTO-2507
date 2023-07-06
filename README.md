# RTO-2507


const responseJson = pm.response.json();
let availabilitiesArePresent = {
  am: false,
  pm: false,
  evening: false
};
pm.test(request.name + "- Status code is 200", function () {
    pm.response.to.have.status(200);
});
let availabilityValidationScript = pm.environment.get("envAvailabilityValidationScript");
eval(availabilityValidationScript);
(pm.variables.get("SKIP") ? pm.test.skip : pm.test)(request.name + "- Verify that preferred moments will return availabilities", () => {
    for (let i = 0; i < responseJson.length; i++) {
        let hour = new Date(responseJson[i].startTime).getHours();
        let ampm = hour < 12 ? "AM" : hour < 17 ? "PM" : "Evening";
        availabilitiesArePresent[ampm.toLowerCase()] = true;
        if (ampm === "PM") {
            break;
        }
    }
    pm.expect(availabilitiesArePresent.pm).to.equal(false);
});
