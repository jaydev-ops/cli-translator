1. Install LM Studio

Download and install LM Studio from:
https://lmstudio.ai

⸻

2. Download Model

Load a model like:

meta-llama-3.1-8b-instruct

⸻

3. Start Local Server

Start the server in LM Studio:

http://127.0.0.1:1234

⸻

4. Install Dependencies
pip install -r requirements.txt

⸻

6. Add this code to ur Zshrc
ask() {
  PROMPT="$*"

  JSON=$(printf '{
    "model": "meta-llama-3.1-8b-instruct",
    "messages": [
      {
        "role": "system",
        "content": "You are a Unix CLI translator. Convert natural language into safe macOS zsh commands. Output ONLY the command. No explanations."
      },
      {
        "role": "user",
        "content": "%s"
      }
    ],
    "temperature": 0.2
  }' "$PROMPT")

  RESPONSE=$(curl -s http://127.0.0.1:1234/v1/chat/completions \
    -H "Content-Type: application/json" \
    -d "$JSON")

  COMMAND=$(echo "$RESPONSE" | python3 -c "
import sys, json
try:
    data = json.load(sys.stdin)
    print(data['choices'][0]['message']['content'].strip())
except:
    print('Error: Failed to parse response')
")

  # Basic safety check
  if [[ "$COMMAND" == *"rm -rf /"* ]]; then
    echo "⚠️ Dangerous command blocked"
    return
  fi

  echo "$COMMAND"
}