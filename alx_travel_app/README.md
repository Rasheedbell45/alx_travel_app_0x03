# alx_travel_app_0x03

This project extends alx_travel_app_0x02 by integrating Celery with RabbitMQ to handle background tasks and adding an email notification feature for bookings.

Features

Celery with RabbitMQ for asynchronous task processing.

Booking confirmation email sent automatically after a booking is created.

Uses Django email backend for email delivery.

Setup Instructions
1. Clone Project
git clone https://github.com/<your-username>/alx_travel_app_0x03.git
cd alx_travel_app_0x03

2. Install Dependencies
pip install -r requirements.txt

3. Configure RabbitMQ

Install RabbitMQ (example for Ubuntu/Debian):

sudo apt update
sudo apt install rabbitmq-server
sudo systemctl enable rabbitmq-server
sudo systemctl start rabbitmq-server


Confirm it is running:

sudo systemctl status rabbitmq-server

4. Configure Django Email Backend

In settings.py, configure your email backend (example using console for dev):

EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
DEFAULT_FROM_EMAIL = "noreply@alxtravel.com"


For production, you can switch to SMTP:

EMAIL_BACKEND = "django.core.mail.backends.smtp.EmailBackend"
EMAIL_HOST = "smtp.gmail.com"
EMAIL_PORT = 587
EMAIL_USE_TLS = True
EMAIL_HOST_USER = "<your-email>@gmail.com"
EMAIL_HOST_PASSWORD = "<your-app-password>"

5. Configure Celery

In alx_travel_app_0x03/celery.py:

import os
from celery import Celery

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'alx_travel_app_0x03.settings')

app = Celery('alx_travel_app_0x03')
app.config_from_object('django.conf:settings', namespace='CELERY')
app.autodiscover_tasks()


In settings.py:

CELERY_BROKER_URL = 'amqp://localhost'
CELERY_RESULT_BACKEND = 'rpc://'


In alx_travel_app_0x03/__init__.py:

from .celery import app as celery_app
__all__ = ('celery_app',)

6. Run Migrations
python manage.py migrate

7. Start Services

Run Django server:

python manage.py runserver


Run Celery worker:

celery -A alx_travel_app_0x03 worker -l info


(Optional) Run Celery beat (if scheduling tasks later):

celery -A alx_travel_app_0x03 beat -l info

8. Test Booking Email Task

Create a booking via API or Django Admin.

A background task will send a booking confirmation email.

Check console (dev) or email inbox (prod).
