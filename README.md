from pathlib import Path
from collections import defaultdict


class CodeAnalyzer:

    EXTENSIONS = {
        ".py": "Python",
        ".js": "JavaScript",
        ".ts": "TypeScript",
        ".java": "Java",
        ".cpp": "C++",
        ".c": "C",
        ".cs": "C#",
        ".go": "Go",
        ".rs": "Rust"
    }

    def __init__(self, project_path):
        self.project_path = Path(project_path)

        self.total_files = 0
        self.total_lines = 0
        self.total_comments = 0

        self.languages = defaultdict(int)

    def analyze(self):

        for file in self.project_path.rglob("*"):

            if not file.is_file():
                continue

            ext = file.suffix.lower()

            if ext not in self.EXTENSIONS:
                continue

            self.total_files += 1

            self.languages[
                self.EXTENSIONS[ext]
            ] += 1

            try:

                with open(
                    file,
                    "r",
                    encoding="utf-8",
                    errors="ignore"
                ) as f:

                    for line in f:

                        line = line.strip()

                        if not line:
                            continue

                        self.total_lines += 1

                        if (
                            line.startswith("#")
                            or line.startswith("//")
                            or line.startswith("/*")
                            or line.startswith("*")
                        ):
                            self.total_comments += 1

            except Exception:
                pass

    def report(self):

        print("\n=== Project Statistics ===\n")

        print(
            f"Files         : {self.total_files}"
        )

        print(
            f"Lines of Code : {self.total_lines}"
        )

        print(
            f"Comments      : {self.total_comments}"
        )

        if self.total_lines:

            ratio = (
                self.total_comments
                / self.total_lines
            ) * 100

            print(
                f"Comment Ratio : "
                f"{ratio:.2f}%"
            )

        print("\nLanguages\n")
        print("-" * 30)

        for lang, count in sorted(
            self.languages.items(),
            key=lambda x: x[1],
            reverse=True
        ):
            print(
                f"{lang:<15} {count}"
            )


def main():

    project = input(
        "Project path: "
    ).strip()

    analyzer = CodeAnalyzer(project)

    print("\nScanning project...\n")

    analyzer.analyze()

    analyzer.report()


if __name__ == "__main__":
    main()
