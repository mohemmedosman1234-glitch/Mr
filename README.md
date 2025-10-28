import asyncio
import jdatetime
import warnings
warnings.filterwarnings("ignore", module="rubpy")
from rubpy import Client
from rubpy.filters import filters
from rubpy.types import Updates
from apscheduler.schedulers.asyncio import AsyncIOScheduler

bot = Client("rubpy")

#اینجا گوید گروه رو وارد کنید
group_guid = "g0F3Kj30ad949d2c2ab97ff036386cff"

#اینجا اسم گروه رو(اسم فارسی نباشه)
group_name = "test hakhaman"

FONTS = {
    "fancy1": {'0': '𝟘', '1': '𝟙', '2': '𝟚', '3': '𝟛', '4': '𝟜', '5': '𝟝', '6': '𝟞', '7': '𝟟', '8': '𝟠', '9': '𝟡', ':': ':'},
    "mono": {'0': '𝟶', '1': '𝟷', '2': '𝟸', '3': '𝟹', '4': '𝟺', '5': '𝟻', '6': '𝟼', '7': '𝟽', '8': '𝟾', '9': '𝟿', ':': ':'},
    "double": {'0': '𝟘', '1': '𝟙', '2': '𝟚', '3': '𝟛', '4': '𝟜', '5': '𝟝', '6': '𝟞', '7': '𝟟', '8': '𝟠', '9': '𝟡', ':': ':'},
    "sans": {'0': '𝟬', '1': '𝟭', '2': '𝟮', '3': '𝟯', '4': '𝟰', '5': '𝟱', '6': '𝟲', '7': '𝟳', '8': '𝟴', '9': '𝟵', ':': ':'},
    "full": {'0': '０', '1': '１', '2': '２', '3': '３', '4': '４', '5': '５', '6': '６', '7': '７', '8': '８', '9': '９', ':': ':'},
    "super": {'0': '⁰', '1': '¹', '2': '²', '3': '³', '4': '⁴', '5': '⁵', '6': '⁶', '7': '⁷', '8': '⁸', '9': '⁹', ':': ':'},
    "sub": {'0': '₀', '1': '₁', '2': '₂', '3': '₃', '4': '₄', '5': '₅', '6': '₆', '7': '₇', '8': '₈', '9': '₉', ':': ':'},
    "circle": {'0': '⓪', '1': '①', '2': '②', '3': '③', '4': '④', '5': '⑤', '6': '⑥', '7': '⑦', '8': '⑧', '9': '⑨', ':': ':'},
    "neg_circle": {'0': '⓪', '1': '①', '2': '②', '3': '③', '4': '④', '5': '⑤', '6': '⑥', '7': '⑦', '8': '⑧', '9': '⑨', ':': ':'
}
}

HOUR_TO_FONT = {
    0: "fancy1", 1: "mono", 2: "double", 3: "sans", 4: "full", 5: "super",
    6: "sub", 7: "circle", 8: "neg_circle", 9: "fancy1", 10: "mono", 11: "double",
    12: "sans", 13: "full", 14: "super", 15: "sub", 16: "circle", 17: "neg_circle",
    18: "fancy1", 19: "mono", 20: "double", 21: "sans", 22: "full", 23: "super"
}

def convert_time(time_str, font_name):
    font = FONTS.get(font_name, FONTS["fancy1"])
    return ''.join(font.get(ch, ch) for ch in time_str)

async def update_group_name():
    try:
        now = jdatetime.datetime.now()
        font_name = HOUR_TO_FONT.get(now.hour, "fancy1")
        fancy_time = convert_time(now.strftime("%H:%M:%S"), font_name)
        new_name = f"{fancy_time} | {group_name}"
        await bot.edit_group_info(group_guid, title=new_name)
    except Exception as e:
        print(f"خطا در تغییر اسم گروه: {e}")

async def main():
    await bot.start()
    scheduler = AsyncIOScheduler(timezone="Asia/Tehran")
    scheduler.add_job(update_group_name, "cron", second="*")
    scheduler.start()
    await asyncio.Event().wait()
if __name__ == "__main__":
    asyncio.run(main())
