python3 --version

brew --version

brew install python@3.12

python3.12 --version


cd ~/Projects/rag-chatbot-llm-eval
python3.12 -m venv .venv
source .venv/bin/activate
python --version

cd rag-chatbot-llm-eval
git init

source .venv/bin/activate
pip install -r requirements.txt

source .venv/bin/activate
python src/rag_bot.py