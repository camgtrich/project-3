
import requests

# Add your own NASA API key here.
API_KEY = "TnJjQpCTPzk8g8hRpq7rgPr8fHwGzJHwDhw7JbDr"


# Function 1: Ask the user for a date.
def get_user_date():
    date = input("Enter a date (YYYY-MM-DD): ")
    return date


# Function 2: Retrieve data from NASA's EPIC API.
def retrieve_epic_photos(api_key, date):
    endpoint_url = f"https://api.nasa.gov/EPIC/api/natural/date/{date}"
    params = {"api_key": api_key}

    response = requests.get(endpoint_url, params=params)

    if response.status_code == 200:
        return response.json()

    elif response.status_code == 403:
        print("Error: API key is missing or invalid.")
    elif response.status_code == 429:
        print("Error: API rate limit reached. Try again later.")
    else:
        print(f"Error: Data not received. Status code: {response.status_code}")

    return None


# Function 3: Count the photos returned.
def count_photos(photos):
    if len(photos) == 0:
        print("No photos were found for this date.")
        return 0

    print(f"Photos found: {len(photos)}")
    return len(photos)


# Function 4: Build a link to an EPIC image.
def build_image_url(photo):
    photo_date = photo.get("date", "").split(" ")[0]
    image_name = photo.get("image")

    if not photo_date or not image_name:
        return None

    year, month, day = photo_date.split("-")

    image_url = (
        f"https://epic.gsfc.nasa.gov/archive/natural/"
        f"{year}/{month}/{day}/png/{image_name}.png"
    )

    return image_url


# Function 5: Display information about each photo.
def display_photo_info(photos):
    for photo in photos:
        print("\n-------------------------")
        print("Date:", photo.get("date", "Unknown"))
        print("Image:", photo.get("image", "Unknown"))
        print("Identifier:", photo.get("identifier", "Unknown"))
        print("Caption:", photo.get("caption", "No caption"))

        image_url = build_image_url(photo)

        if image_url:
            print("Image link:", image_url)


# Main program: Runs all the functions.
def main():
    date = get_user_date()

    photos = retrieve_epic_photos(API_KEY, date)

    if photos is None:
        return

    photo_count = count_photos(photos)

    if photo_count == 0:
        return

    display_photo_info(photos)


if __name__ == "__main__":
    main()
