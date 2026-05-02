- name: Build artifact
  run: |
    mkdir -p build
    cp app.py build/
    zip -r "$ARTIFACT_NAME" build/
    ls -lh "$ARTIFACT_NAME"
