# Dockerfile
FROM python:3 AS builder

RUN git clone https://github.com/chuyangliu/snake.git

WORKDIR ./snake

# RUN head -n -2 ./tests/gui/test_gui.py > temp.py && mv temp.py ./tests/gui/test_gui.py

RUN pip install -r requirements.txt

RUN pip install pyinstaller

FROM builder AS tester

COPY --from=builder /snake /snake

CMD ["python", "-m", "pytest"]