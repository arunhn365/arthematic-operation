import os
import zipfile

DOCS_DIR = 'documents/'
ARCHIVE_NAME = 'documents_archive.zip'

def create_document(filename, content):
    with open(os.path.join(DOCS_DIR, filename), 'w') as f:
        f.write(content)
    print(f'Document created: {filename}')

def list_documents():
    print('Documents:')
    for filename in os.listdir(DOCS_DIR):
        print(f' - {filename}')

def update_document(filename, new_content):
    try:
        with open(os.path.join(DOCS_DIR, filename), 'w') as f:
            f.write(new_content)
        print(f'Document updated: {filename}')
    except FileNotFoundError:
        print(f'Document not found: {filename}')

def delete_document(filename):
    try:
        os.remove(os.path.join(DOCS_DIR, filename))
        print(f'Document deleted: {filename}')
        create_archive()  # Recreate the archive after deletion
    except FileNotFoundError:
        print(f'Document not found: {filename}')

def create_archive():
    with zipfile.ZipFile(ARCHIVE_NAME, 'w') as archive:
        for foldername, _, filenames in os.walk(DOCS_DIR):
            for filename in filenames:
                file_path = os.path.join(foldername, filename)
                archive.write(file_path, os.path.relpath(file_path, DOCS_DIR))
    print(f'Created/Updated archive: {ARCHIVE_NAME}')

def list_archived_files():
    print('Archived files:')
    with zipfile.ZipFile(ARCHIVE_NAME, 'r') as archive:
        for file_info in archive.infolist():
            print(f' - {file_info.filename}')

def extract_file(file_name):
    with zipfile.ZipFile(ARCHIVE_NAME, 'r') as archive:
        try:
            archive.extract(file_name, DOCS_DIR)
            print(f'Extracted: {file_name}')
        except KeyError:
            print(f'File not found in archive: {file_name}')

def open_archive():
    print("Opening archive...")
    list_archived_files()

def open_document(filename):
    try:
        with open(os.path.join(DOCS_DIR, filename), 'r') as f:
            content = f.read()
        print(f'Contents of {filename}:\n{content}')
    except FileNotFoundError:
        print(f'Document not found: {filename}')

def main():
    while True:
        print("\nDocument Management System")
        print("1. Create Document")
        print("2. List Documents")
        print("3. Open Document")
        print("4. Update Document")
        print("5. Delete Document")
        print("6. Create Archive")
        print("7. Open Archive")
        print("8. Extract File from Archive")
        print("9. Exit")

        choice = input("Choose an option: ")

        if choice == '1':
            filename = input("Enter the document name: ")
            content = input("Enter the document content: ")
            create_document(filename, content)
        elif choice == '2':
            list_documents()
        elif choice == '3':
            filename = input("Enter the document name to open: ")
            open_document(filename)
        elif choice == '4':
            filename = input("Enter the document name to update: ")
            new_content = input("Enter the new content: ")
            update_document(filename, new_content)
        elif choice == '5':
            filename = input("Enter the document name to delete: ")
            delete_document(filename)
        elif choice == '6':
            create_archive()
        elif choice == '7':
            open_archive()
        elif choice == '8':
            file_name = input("Enter the name of the file to extract: ")
            extract_file(file_name)
            # Do not list documents in the archive after extraction
        elif choice == '9':
            break
        else:
            print("Invalid option, please try again.")

if __name__ == "__main__":
    if not os.path.exists(DOCS_DIR):
        os.makedirs(DOCS_DIR)  # Create documents directory if it doesn't exist
    main()
