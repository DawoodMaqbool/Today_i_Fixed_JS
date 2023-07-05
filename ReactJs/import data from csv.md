## Problem

How to upload a csv file in React and send file data to backend.

## Environment

- IDE Version: Visual Studio Code

## How you fix it

We have used papaparse library to parse the data in the csv file and formatted it before sending it to backend.

## Solution

```
import React, {useState} from 'react';
import {Button, Upload, Modal, message} from 'antd';
import Papa from 'papaparse';
import axios from 'axios';


export const extractFileList = (event) => {
  const list = event?.fileList;
  let file = event?.fileList && event?.fileList[0];

  if (file) {
    file = {...file, status: 'done'};
    list[0] = file;
  }

  return list;
};

const FileUploadModal = () => {
  const [uploadedFile, setUploadedFile] = useState();
  const [csvData, setCsvData] = useState([]);

  const handleUpload = (file) => {
    const reader = new FileReader();
    reader.readAsText(file);

    reader.onload = () => {
      const {data} = Papa.parse(reader.result, {
        header: true,
        skipEmptyLines: true,
      });
      setCsvData(data);
    };
  };

  const beforeUpload = (file) => {
    const isFile = file.type === 'text/csv';

    if (!isFile) {
      message.error('You can only upload CSV file!');
    }

    handleUpload(file);
    return isFile;
  };

  const handleChange = async (event) => {
    const list = extractFileList(event);

    setUploadedFile(list);
  };

  const submit = () => {
    const formData = new FormData();

    const data = JSON.stringify(csvData).toString();

    formData.append('fileData', data);

    <!-- Send form data to backend -->
    axios.post('{BaseUrl}/import`, body, {
        'Content-Type': 'application/json',
      });
  };

  return (
    <>
      <Upload.Dragger
        action={null}
        customRequest={() => {}}
        listType='text'
        className='uploadBox'
        accept='text/csv'
        onChange={handleChange}
        beforeUpload={beforeUpload}
        onRemove={() => setCsvData([])}>
        Drag file here OR
        <br />
        <Button className='uploadBtn'>Upload File</Button>
      </Upload.Dragger>
      <div className='btnWrapper'>
        <Button
          disabled={!uploadedFile}
          type='primary'
          onClick={() => submit()}>
          Submit
        </Button>
      </div>
    </>
  );
};

export default FileUploadModal;

```
