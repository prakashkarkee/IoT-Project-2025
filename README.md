# IoT-Project-2025
IoT Project 2025

BMP280 generates temperature measurements, which are sent to HiveMQ. That data is fetched for logic functions in node-red for alert text messages using nexmo if out of threshold and pushed to the InfluxDB for trend analysis.

main = Main sensor to mqtt file created by Mahela Ranasinghe.
Logic nodes node-red = Logic node codes for the MQTT to InfluxDB and alert logic within node-red.
