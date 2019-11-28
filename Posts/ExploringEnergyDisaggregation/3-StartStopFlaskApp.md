# Use systemctl to control Flask App
We've been running [our Flask app](https://github.com/BitKnitting/FitHome_mongodb/tree/master/flask_readings) within a venv and not as a service on our Rasp Pi.  It's time to make it a service so it can run in the background, be stopped/started, and all the other good stuff that comes with using [systemd services](https://github.com/BitKnitting/should_I_water/wiki/systemd-services) bring.