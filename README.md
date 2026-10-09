import os
import shutil
from pathlib import Path
from datetime import datetime
TARGET_FOLDER = r"C:\Users\ВскВолоД\Desktop\пайтон"
LOG_FILE = "log.txt"
EXTENSION_MAP = {
    ".jpg": "Images", ".jpeg": "Images", ".png": "Images", ".gif": "Images", ".bmp": "Images",
    ".pdf": "Docs", ".doc": "Docs", ".docx": "Docs", ".txt": "Docs", ".rtf": "Docs",
    ".exe": "Programs", ".msi": "Programs", ".bat": "Programs", ".sh": "Programs",
    ".zip": "Archives", ".rar": "Archives", ".7z": "Archives", ".tar": "Archives",
    ".mp3": "Audio", ".wav": "Audio", ".flac": "Audio",
    ".mp4": "Video", ".avi": "Video", ".mkv": "Video", ".mov": "Video",
}
def get_category(ext: str) -> str:
    ext = ext.lower()
    return EXTENSION_MAP.get(ext, "Other")

def ensure_dir(path: Path) -> None:
    if not path.exists():
        path.mkdir(parents=True, exist_ok=True)
def log_message(message: str, log_path: Path) -> None:
    timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    with open(log_path, "a", encoding="utf-8") as f:
        f.write(f"[{timestamp}] {message}\n")
def sort_files(target_folder: str, log_file: str) -> None:
    target_path = Path(target_folder)
    log_path = target_path / log_file
    if not target_path.exists():
        print(f"Ошибка: папка не найдена — {target_path}")
        return
    for item in target_path.iterdir():
        if item.is_file():
            ext = item.suffix
            category = get_category(ext)
            dest_dir = target_path / category
            ensure_dir(dest_dir)
            dest_path = dest_dir / item.name
            if dest_path.exists():
                base, suffix = item.stem, item.suffix
                counter = 1
                while dest_path.exists():
                    new_name = f"{base}_{counter}{suffix}"
                    dest_path = dest_dir / new_name
                    counter += 1
            try:
                shutil.move(str(item), str(dest_path))
                log_msg = f"Перемещено: {item.name} → {dest_path.relative_to(target_path)}"
                log_message(log_msg, log_path)
                print(log_msg)
            except Exception as e:
                error_msg = f"Ошибка при перемещении {item.name}: {e}"
                log_message(error_msg, log_path)
                print(error_msg)
if __name__ == "__main__":
    sort_files(TARGET_FOLDER, LOG_FILE)
