<template>
  <div class="mx-auto text-center p-4 ">
    <div class="sticky-container">
      <h1 class="text-3xl font-semibold mb-4">Weekly Schedule</h1>
      <p class="text-sm text-gray-500 mb-2">*All included for in-house guests.</p>
      <p class="text-lg mb-16">Advanced booking is required.</p>
      <div class="overflow-x-auto">
        <div class="schedule-grid">
          <div class="p-6 border border-gray-300 border-x-0 border-t-0 time-slot"></div>
          <div v-for="day in days" :key="day" class="p-6 border border-gray-300 border-r-0 border-y-0 day">{{ day }}</div>
          <div v-for="time in timeSlots" :key="time" class="grid-row">
            <div class="pt-8 border border-gray-300 time-slot border-x-0 border-t-0 align-middle opacity-100">{{ formatTime(time) }}</div>
            <div v-for="day in days" :key="day + time" class="relative p-2 h-20 border border-gray-300 border-r-0 border-b-0">
              <h4 v-if="getActivity(day, time)" class="m-auto p-1 custom-width" :style="getEventStyle(getActivity(day, time), time)">
                {{ getActivity(day, time).name }}
              </h4>
            </div>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
  name: "WeeklySchedule",
  setup() {
    const activities = [
      {
        name: "Sunrise Yoga",
        color: "#E5DCCA",
        times: [
          { day: "Monday", startTime: 7, endTime: 7.5 },
          { day: "Wednesday", startTime: 7, endTime: 7.5 },
          { day: "Friday", startTime: 7, endTime: 7.5 }
        ]
      },
      {
        name: "Morning Yoga",
        color: "#E0E5D2",
        times: [
          { day: "Monday", startTime: 9, endTime: 10 },
          { day: "Tuesday", startTime: 9, endTime: 10 },
          { day: "Wednesday", startTime: 9, endTime: 10 },
          { day: "Thursday", startTime: 9, endTime: 10 },
          { day: "Friday", startTime: 9, endTime: 10 }
        ]
      },
      {
        name: "Aqua Stretching",
        color: "#EDD2A0",
        times: [
          { day: "Monday", startTime: 10.5, endTime: 11.25 },
          { day: "Wednesday", startTime: 10.5, endTime: 11.25 },
          { day: "Friday", startTime: 10.5, endTime: 11.25 }
        ]
      },
      {
        name: "Balinese Offerings",
        color: "#E5D0A7CE",
        times: [
          { day: "Monday", startTime: 11, endTime: 14 },
          { day: "Tuesday", startTime: 11, endTime: 14 },
          { day: "Wednesday", startTime: 11, endTime: 14 },
          { day: "Friday", startTime: 11, endTime: 14 }
        ]
      },
      {
        name: "Sounds Healing",
        color: "#CED5CE",
        times: [
          { day: "Monday", startTime: 14, endTime: 15.2 },
          { day: "Tuesday", startTime: 14, endTime: 15.2 },
          { day: "Wednesday", startTime: 14, endTime: 15.2 },
          { day: "Thursday", startTime: 14, endTime: 15.2 },
          { day: "Friday", startTime: 14, endTime: 15.2 },
          { day: "Saturday", startTime: 14, endTime: 15.2 },
          { day: "Sunday", startTime: 14, endTime: 15.2 }
        ]
      },
      {
        name: "Sunset Yoga",
        color: "#E3E5CD",
        times: [
          { day: "Monday", startTime: 18, endTime: 19 },
          { day: "Wednesday", startTime: 18, endTime: 19 },
          { day: "Thursday", startTime: 18, endTime: 19 },
          { day: "Friday", startTime: 18, endTime: 19 },
          { day: "Saturday", startTime: 18, endTime: 19 }
        ]
      },
      {
        name: "Alu Trekking",
        color: "#C2C9AB",
        times: [
          { day: "Tuesday", startTime: 10.5, endTime: 14 },
          { day: "Thursday", startTime: 10.5, endTime: 14 },
          { day: "Saturday", startTime: 10.5, endTime: 14 }
        ]
      }
    ];

    const generateTimeSlots = (activities) => {
      const timeSlotSet = new Set();
      activities.forEach(activity => {
        activity.times.forEach(time => {
          timeSlotSet.add(Math.floor(time.startTime));
          if ((time.endTime - time.startTime) > 1) {
            timeSlotSet.add(Math.floor(time.endTime));
          }
        });
      });
      return Array.from(timeSlotSet).sort((a, b) => a - b);
    };

    const data = {
      days: ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"],
      timeSlots: generateTimeSlots(activities)
    };

    const getActivity = (day, time) => {
      return activities.find(activity =>
        activity.times.some(slot => slot.day === day && Math.floor(slot.startTime) === time)
      );
    };

    const calculateHeightAndPosition = (startTime, endTime, timeSlots) => {
      const duration = endTime - startTime;
      let height;
      if (duration >= 2) {
        const startIndex = timeSlots.indexOf(Math.floor(startTime));
        const endIndex = timeSlots.indexOf(Math.floor(endTime));

        if(Number.isInteger(startTime) === true) {
          height = `${(endIndex - startIndex) * 4}rem`;
        } else {
          height = `${((endIndex - (startIndex+1)) + (startTime % 1))* 4 + 1}rem`;
        }
      }
      else if (duration >= 1 && duration < 2) {
        height = `${duration * 4}rem`;
      } else {
        height = `${duration * 4}rem`;
      }

      let startPos;
      if (Number.isInteger(startTime) !== true) {
        startPos = (startTime - parseInt(startTime)) * 4;
      } else {
        startPos = (startTime % 1) * 4;
      }

      return { height, marginTop: `${startPos}rem` };
    };

    const getEventStyle = (activity, time) => {
      const activityTime = activity.times.find(slot => Math.floor(slot.startTime) === time);
      if (activityTime) {
        const { startTime, endTime, day } = activityTime;
        const { height, marginTop } = calculateHeightAndPosition(startTime, endTime, data.timeSlots);

        const overlappingActivities = activities.filter(a =>
          a.times.some(t => t.day === day && t.startTime < endTime && t.endTime > startTime)
        );

        const totalActivities = overlappingActivities.length;
        const index = overlappingActivities.findIndex(a => a.name === activity.name);

        let width = '90%';
        let left = '5%';

        if (totalActivities > 1) {
          width = `${90 / totalActivities}%`;
          left = `${5 + index * (90 / totalActivities)}%`;
        }

        return {
          backgroundColor: activity.color,
          height,
          marginTop,
          width,
          left
        };
      }
      return {};
    };

    const formatTime = (time) => {
      const hour = Math.floor(time);
      const suffix = hour >= 12 ? 'PM' : 'AM';
      const formattedHour = hour % 12 || 12;
      // const minutes = (time % 1) * 60;
      // const formattedMinutes = minutes === 0 ? '00' : minutes;
      return `${formattedHour}:${suffix}`;
    };

    return {
      ...data,
      getActivity,
      getEventStyle,
      formatTime
    };
  }
});
</script>

<style scoped>
.custom-width {
  z-index: 9;
}

.sticky-container {
  position: sticky;
  left: 0;
  max-width: 1440px;
  margin: 0 auto;
  top: 0;
}

.schedule-grid {
  display: grid;
  grid-template-columns: 80px repeat(7, 1fr);
  gap: 0;
  width: 1440px;
}

.schedule-grid > .grid-row:last-child .activity-cell, .schedule-grid > .grid-row:last-child .time-slot {
  border-bottom: none;
}

.time-slot {
  letter-spacing: 0.7px;
  color: #AD8C48;
  background-color: #EFEBE2;
  position: sticky;
  left: 0;
  z-index: 10;
}

.day {
  font: normal normal medium 14px/1px Basis Grotesque Arabic Pro;
  letter-spacing: 0px;
  color: #AD8C48;
  background-color: #EFEBE2;
  border-bottom: 1px solid rgba(173, 140, 72, 0.5);
}

.grid-row {
  display: contents;
}

.activity-cell {
  border: 1px solid rgba(173, 140, 72, 0.5);
}

h4 {
  padding: 4px;
  border-radius: 5px;
  font: normal normal medium 12px/35px Basis Grotesque Arabic Pro;
  position: absolute;
}

h1 {
  font: normal normal normal 40px/50px Americana;
  letter-spacing: 0px;
  color: #AD8C48;
  opacity: 1;
}

.border-gray-300 {
  border-color: rgba(173, 140, 72, 0.5);
}

/* @media screen and (max-width: 1440px) {
  .sticky-container {
    width: 1440px;
    position: sticky;
    left: 0;
  }
} */
</style>
