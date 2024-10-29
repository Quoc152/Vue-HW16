<template>
  <div class="min-h-screen bg-gray-100 flex items-center justify-center p-4">
    <Card class="w-full max-w-md">
      <CardHeader class="text-center">
        <CardTitle class="mb-2">Đăng ký nhân viên mới</CardTitle>
        <CardDescription
          >Vui lòng điền đầy đủ thông tin nhân viên</CardDescription
        >
      </CardHeader>
      <CardContent>
        <form @submit="onSubmit">
          <div class="space-y-4">
            <div class="space-y-2">
              <Label for="fullName">Họ và tên:</Label>
              <Input
                id="fullName"
                v-model="fullName"
                placeholder="Tên..."
              />
              <ErrorMessage class="text-sm text-red-500" name="fullName" />
            </div>

            <div class="space-y-2">
              <Label for="email">Email công ty:</Label>
              <Input
                id="email"
                v-model="email"
                type="email"
                placeholder="example@company.com"
              />
              <ErrorMessage class="text-sm text-red-500" name="email" />
            </div>

            <div class="space-y-2">
              <Label for="phone">Số điện thoại:</Label>
              <Input id="phone" v-model="phone" placeholder="0123456789" />
              <ErrorMessage class="text-sm text-red-500" name="phone" />
            </div>

            <div class="space-y-2">
              <Label for="birthDate">Ngày sinh:</Label>
              <Input id="birthDate" v-model="birthDate" type="date" />
              <ErrorMessage class="text-sm text-red-500" name="birthDate" />
            </div>

            <div class="space-y-2">
              <Label for="position">Vị trí công việc:</Label>
              <Input
                id="position"
                v-model="position"
                placeholder="Nhân viên kinh doanh"
              />
              <ErrorMessage class="text-sm text-red-500" name="position" />
            </div>

            <div class="space-y-2">
              <Label for="department">Phòng ban:</Label>
              <Select v-model="department">
                <SelectTrigger>
                  <SelectValue placeholder="Chọn phòng ban" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="sales">Kinh doanh</SelectItem>
                  <SelectItem value="marketing">Marketing</SelectItem>
                  <SelectItem value="it">Công nghệ thông tin</SelectItem>
                  <SelectItem value="hr">Nhân sự</SelectItem>
                </SelectContent>
              </Select>
              <ErrorMessage class="text-sm text-red-500" name="department" />
            </div>

            <div class="space-y-2">
              <Label for="startDate">Ngày bắt đầu làm việc:</Label>
              <Input id="startDate" v-model="startDate" type="date" />
              <ErrorMessage class="text-sm text-red-500" name="startDate" />
            </div>
          </div>

          <Button type="submit" class="w-full mt-6">Đăng ký</Button>
        </form>
      </CardContent>
    </Card>
  </div>

  <Dialog v-model:open="dialogOpen">
    <DialogContent>
      <DialogHeader>
        <DialogTitle>Thông tin đăng ký</DialogTitle>
        <DialogDescription>
          Dưới đây là thông tin bạn vừa nhập:
        </DialogDescription>
      </DialogHeader>
      <div class="space-y-2">
        <p v-for="(value, key) in formValues" :key="key">
          <strong>{{ formatFieldName(key) }}:</strong> {{ value }}
        </p>
      </div>
      <DialogFooter>
        <Button @click="dialogOpen = false">Đóng</Button>
      </DialogFooter>
    </DialogContent>
  </Dialog>
</template>

<script setup>
import { ref, computed } from "vue";
import { useForm, useField } from "vee-validate";
import { toTypedSchema } from "@vee-validate/zod";
import * as z from "zod";
import {
  Card,
  CardHeader,
  CardTitle,
  CardDescription,
  CardContent,
} from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
} from "@/components/ui/dialog";
import { ErrorMessage } from "vee-validate";

const dialogOpen = ref(false);

const schema = toTypedSchema(
  z.object({
    fullName: z.string().min(2, "Họ và tên phải có ít nhất 2 ký tự"),
    email: z.string().email("Email không hợp lệ"),
    phone: z.string().regex(/^[0-9]{10}$/, "Số điện thoại phải có 10 chữ số"),
    birthDate: z
      .string()
      .refine((date) => !isNaN(Date.parse(date)), "Ngày sinh không hợp lệ"),
    position: z.string().min(2, "Vị trí công việc phải có ít nhất 2 ký tự"),
    department: z.string().min(1, "Vui lòng chọn phòng ban"),
    startDate: z
      .string()
      .refine(
        (date) => !isNaN(Date.parse(date)),
        "Ngày bắt đầu làm việc không hợp lệ"
      ),
  })
);

const { handleSubmit, values } = useForm({
  validationSchema: schema,
});

const { value: fullName } = useField("fullName");
const { value: email } = useField("email");
const { value: phone } = useField("phone");
const { value: birthDate } = useField("birthDate");
const { value: position } = useField("position");
const { value: department } = useField("department");
const { value: startDate } = useField("startDate");

const formValues = computed(() => ({
  fullName: fullName.value,
  email: email.value,
  phone: phone.value,
  birthDate: birthDate.value,
  position: position.value,
  department: department.value,
  startDate: startDate.value,
}));

const onSubmit = handleSubmit((values) => {
  console.log(values);
  dialogOpen.value = true;
});

const formatFieldName = (key) => {
  const fieldNames = {
    fullName: "Họ và tên",
    email: "Email công ty",
    phone: "Số điện thoại",
    birthDate: "Ngày sinh",
    position: "Vị trí công việc",
    department: "Phòng ban",
    startDate: "Ngày bắt đầu làm việc",
  };
  return fieldNames[key] || key;
};
</script>
