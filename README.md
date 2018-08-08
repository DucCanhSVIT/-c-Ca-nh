# DucCanh
var webdriver = require('selenium-webdriver');
var assert = require('chai').assert;
 
var remoteHub = 'http://hub.crossbrowsertesting.com:80/wd/hub';
 
var username = 'your.email@address.com';
var authkey = 'yourAuthKey';
 
var caps = {
    name: 'GitHub Search',
    build: '1.0.0',
    browserName: 'MicrosoftEdge',
    version: '15',
    platform: 'Windows 10',
    screen_resolution: '1366x768',
    record_video: 'true',
    record_network: 'true',
    username: username,
    password: authkey
};
 
describe("Searching GitHub for Mocha", function () {
    this.timeout(5 * 1000 * 60);
    var driver = new webdriver.Builder()
        .usingServer(remoteHub)
        .withCapabilities(caps)
        .build();
    before(function setupWebdriver(done) {
        driver.get("https://github.com/search/advanced").then(done)
    });
    it("Mochajs Should be the Top Result", function (done) {
        var inputField = driver.findElement(webdriver.By.css(".search-form-fluid .search-page-input"));
        inputField.click()
            .then(function () {
                inputField.sendKeys("Mocha");
            });
        driver.findElement(webdriver.By.css("#search_form button")).click()
            .then(function () {
                return driver.wait(webdriver.until.elementLocated(webdriver.By.css(".repo-list h3 a")), 10000)
            })
            .then(function (element) {
                return element.getText();
            })
            .then(function (text) {
                assert.deepEqual(text, "mochajs/mocha");
            })
            .then(done);
    });
    it("Should Show a Sign Up Prompt after Loading the Repository Page", function (done) {
        driver.findElement(webdriver.By.css(".repo-list h3 a")).click()
            .then(function () {
                return driver.wait(webdriver.until.elementLocated(webdriver.By.css(".signup-prompt h3.pt-2")), 10000)
            })
            .then(function (element) {
                return element.getText();
            })
            .then(function (text) {
                assert.deepEqual(text, "Join GitHub today");
            });
        driver.findElement(webdriver.By.css(".signup-prompt form button")).click()
            .then(done);
    });
    after(function quitWebdriver(done) {
        driver.quit()
            .then(done);
    });
});
